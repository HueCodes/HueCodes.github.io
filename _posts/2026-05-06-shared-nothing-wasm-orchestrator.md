---
layout: post
title: "Designing a Shared-Nothing Wasm Orchestrator"
date: 2026-05-06
category: posts
---

The MicroVM was the right answer to the wrong question. It asked "how do we get VM-grade isolation with container-grade startup?" and the answer was Firecracker, a stripped device model, and cold starts that bottomed out around 125ms. For workloads that live for hours, that's free. For request-scoped edge compute, where a function lives 4ms and serves one HTTP request, 125ms of cold-path jitter isn't a tax you amortize. It's the budget.

WebAssembly flips the constants. A precompiled `wasmtime` module instantiates in **tens of microseconds**. Isolation comes from compile-time bounds checks on the linear memory and module-level type safety, not from a vmexit and a second-stage page-table walk. Density goes from hundreds of MicroVMs per host to tens of thousands of Wasm instances. The interesting platform work is now below the VM, and the orchestrator that lives there has to be designed against the silicon, not against an event loop.

## Pin every thread, share nothing

A 64-core EPYC has eight CCDs, each with its own L3. Cross-CCD traffic is a coherence event in tens of nanoseconds. The default Tokio multi-thread runtime is a work-stealing scheduler: a task wakes on core 3, gets stolen onto core 47, and now its working set is cold in L1, cold in L2, on the wrong NUMA node, and the connection state it just touched is sitting on a different L3 entirely. Work stealing optimizes mean throughput at the cost of variance. For p99-sensitive workloads that's the wrong trade.

The shared-nothing alternative is mechanical:

- One OS thread per physical core, pinned with `libc::sched_setaffinity`. Pin the NIC RX-queue IRQs to the same cores while you're at it, so the packet and its handler share L1.
- Each thread owns its io_uring, slab allocator, `wasmtime::Engine`, and connection table.
- **No locks on the data plane.** No `Arc<Mutex<_>>`, no atomic refcounts in the hot loop.
- Control plane (config, scheduling, draining) runs on a separate thread and reaches data-plane cores through bounded SPSC queues. It's allowed to be slow. The data plane isn't.

## Let the kernel route the flow

How does a connection actually reach a core in this model? `SO_REUSEPORT` with a CBPF steering filter. Each core opens its own listening socket. The kernel hashes the 4-tuple and pins the flow to one socket for its lifetime. No accept-queue contention, no cross-core wakeups, no rebalancing. The connection state, the io_uring CQEs, and the `wasmtime::Store` all live on the same L1.

## The syscall is the budget

`epoll` was designed when a syscall cost a few hundred cycles. With KPTI and retpolines, you're closer to a thousand. At a million RPS per core, that's the budget.

`io_uring` collapses the model. Two ring buffers, submission and completion, mapped into userspace. Set up `IORING_SETUP_SQPOLL` plus `IORING_SETUP_IOPOLL` and the kernel polls the SQ while the device polls completions. The data plane runs without a single `enter` syscall on the hot path.

The hard part is **memory ownership**. A `recv` SQE hands a buffer pointer to the kernel until the matching CQE arrives. Rust's lifetimes don't have a way to say "borrowed by the kernel for an unbounded duration." Two patterns:

- **Owned buffers.** Submission API takes `Vec<u8>` by value, returns it on completion. Safe, but every buffer is a heap allocation.
- **Pre-registered buffers (`IORING_REGISTER_BUFFERS`).** A per-core slab of fixed-size buffers, registered once, referenced by index. The kernel DMAs into them via `IORING_OP_READ_FIXED`. Zero per-request allocation.

Back the registered pool with 2MB hugepages. The TLB footprint of a 1GB region drops from 256K entries to 512, which removes the dTLB miss as a tail-latency contributor.

## Linear memory is the io_uring buffer

This is the seam between I/O and execution, and it's the part most designs get wrong. The NIC has DMA'd a packet into a registered buffer. Wasmtime runs inside a separate linear-memory `mmap`. A naive design copies bytes from the io_uring buffer into linear memory at every dispatch. That copy dominates at small payload sizes and pollutes the L1 line that just arrived warm from the NIC.

The move is to register a fixed prefix of each instance's linear memory **as** the io_uring buffer. The kernel writes directly into guest memory. The Wasm entry function sees its input at a known offset. Zero copies, one TLB-warm page, no host-to-guest pointer translation. The cost is coupling: buffer registration is now tied to instance lifetime, so reset has to re-register or reuse a slot.

## AOT, static memory, epoch interruption

- Modules are **AOT-compiled** at deploy time with Cranelift, serialized to disk, and `mmap`'d at instance creation. The data plane never invokes the compiler.
- Each core's `Engine` is built with `async_support(false)`, `static_memory_maximum_size(4GB)`, and `static_memory_guard_size(2GB)`. Static memory eliminates the bounds-check trampolines that dynamic memories need. The 2GB guard region lets Cranelift fold most loads into a single signal-handled fault.
- Tenant fairness uses **epoch-based interruption**, not fuel. An epoch is one atomic load checked at function entry and loop backedges. Fuel charges every Wasm instruction. For a 50K-instruction request that's 50K extra branches with fuel and roughly 20 with epoch.
- A per-core instance pool reuses `Store`s within a tenant. Reset zeroes linear memory, resets globals, refreshes the epoch budget. Cheaper than a full instantiation. Safe inside the tenant.

Why not JIT and let Cranelift run on a different core? There is no different core. Every core is pinned and serving requests. JIT means tenant code schedules compilation onto the same data-plane core that's holding a flow's state, and Cranelift isn't constant-time. AOT pushes that cost to deploy. Done.

## Throughput alone is the wrong benchmark

The number that matters is **throughput vs p99.9 latency**, not throughput alone. X axis: offered RPS per core. Y axis: p99.9. Three lines:

- A standard Tokio + hyper server, multi-thread runtime.
- A thread-per-core Tokio variant (`tokio-uring` or `monoio`) running the same handler natively.
- The design above, running the same handler compiled to Wasm.

Native thread-per-core wins the absolute throughput number. Wasm pays a ~10-15% tax for the sandbox and gets it back in density and isolation. The default Tokio line diverges hard above 60% utilization because work stealing introduces tail-latency cliffs that pinned cores don't have.

## The last syscall is the network stack

The remaining hot-path syscall is the one userspace can't remove from above: the kernel network stack itself. A packet enters the NIC, traverses the kernel's IP and TCP layers, and lands in a socket buffer before io_uring sees it.

The next move is to push L4 termination into **eBPF/XDP**, do TLS in userspace with kernel TLS offload for the bulk path, and let the orchestrator see only dispatch-ready connections. At that point the kernel is a driver, not a participant.
