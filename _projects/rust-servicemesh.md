---
layout: post
title: "Rust-ServiceMesh: High-Performance L7 Proxy"
---

Service mesh data plane with circuit breakers, rate limiting, and gRPC support. Built on Tokio, Hyper, and Tower.

![Proxy Architecture](/assets/images/projects/servicemesh-architecture.png)

A service mesh proxy sits between your services, handling cross-cutting concerns: retries, timeouts, circuit breaking, load balancing, observability. Instead of implementing this in every service, you deploy a proxy sidecar that handles it transparently.

This project is the data plane. It handles HTTP/1.1, HTTP/2, and gRPC traffic with sub-millisecond overhead.

## Request flow

1. **TLS termination**: ALPN negotiates HTTP/1.1 or HTTP/2
2. **Rate limiting**: Token bucket check per client IP
3. **Routing**: Match request to upstream based on path, headers, or method (regex supported)
4. **Circuit breaker**: Check upstream health, fail fast if open
5. **Load balancing**: Select backend (round-robin, least-conn, random, weighted)
6. **Retry**: Exponential backoff with jitter on failure
7. **Upstream connection**: Forward request, stream response back

Each step is a Tower middleware layer. Layers compose cleanly. Add or remove functionality without touching other code.

HTTP/2 multiplexing was complex. Multiple requests share one connection. Backpressure, flow control, and stream prioritization all needed handling. Hyper does the heavy lifting but configuration matters.

Tower's service trait took time to internalize. Everything is `Service<Request, Response=Response, Error=Error>`. Once it clicks, composition is powerful. Before it clicks, the type errors are cryptic.

## Circuit breaker

![Circuit Breaker States](/assets/images/projects/servicemesh-circuit-breaker.png)

The circuit breaker prevents cascade failures. When an upstream starts failing, stop sending it traffic.

Three states:
- **Closed**: Normal operation. Track failure rate.
- **Open**: Too many failures. Reject requests immediately with 503.
- **Half-Open**: After timeout, allow probe requests. Success closes the circuit, failure reopens.

Each upstream has its own circuit breaker. One failing backend doesn't affect healthy ones. Configuration is per-route: failure threshold (5), success threshold (2), timeout (30s).

## Lock-free internals

The proxy handles thousands of concurrent connections. Traditional locking creates contention.

**DashMap** for routing tables: concurrent hashmap with fine-grained locking. Reads don't block each other.

**Atomics** for circuit breakers: state machine transitions use compare-and-swap. No locks.

**Arc** for shared config: read-heavy, write-rare data uses atomic reference counting. Config updates swap the Arc pointer.

Result: 52M ops/sec on the circuit breaker path. Locking would cap around 5M.

## Performance

| Component | Throughput |
|-----------|------------|
| Circuit breaker | 52M ops/sec |
| Router (exact match) | 30M ops/sec |
| Rate limiter | 20M ops/sec |

Finding edge cases required property-based testing. Random request sequences, random failures, assert invariants hold. Found several bugs unit tests missed.

## Limitations

Configuration is YAML files with no hot reloading. An xDS-compatible control plane interface would enable integration with Istio/Envoy ecosystems. Distributed tracing (Jaeger/Zipkin span propagation) would be the first addition for production use.

---

[View on GitHub](https://github.com/HueCodes/Rust-ServiceMesh)
