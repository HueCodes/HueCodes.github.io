---
layout: post
title: "Understanding Semaphores in Rust"
date: 2026-02-16
category: posts
---

Synchronization primitive for controlling concurrent access to shared resources through permit counting.

Rust's standard library provides Mutex, RwLock, Condvar, channels, and atomics. No semaphore. This is intentional. The stdlib ships minimal concurrency primitives, pushing higher-level abstractions to the ecosystem. Tokio provides tokio::sync::Semaphore. The parking_lot crate offers alternatives. But understanding the underlying mechanism matters.

Semaphores appear everywhere in production systems. Connection pools, API rate limiting, worker thread bounds. Every system that limits concurrent resource access implements a semaphore, whether explicitly named or not.

```rust
// Connection pool is a semaphore
let connection_pool = Pool::new(max_connections);
let conn = pool.get().await; // blocks if pool exhausted
```

## Semaphore Fundamentals

A semaphore maintains a count of available permits. Two operations:

**Acquire** - Decrement the count. If zero, block until a permit becomes available.

**Release** - Increment the count. Wake a waiting thread if any exist.

Edsger Dijkstra introduced semaphores in 1965. The original operations were P (Dutch proberen, "to try") and V (verhogen, "to increment"). This notation still appears in academic papers and legacy codebases.

### Counting vs Binary Semaphores

A counting semaphore allows N concurrent accessors. Initialize with 10 permits, 10 threads can hold the resource. The 11th blocks until someone releases.

```
Semaphore(3):  [■ ■ ■]  <- 3 permits available
Thread A acquires: [■ ■ □]  <- 2 remaining
Thread B acquires: [■ □ □]  <- 1 remaining
Thread C acquires: [□ □ □]  <- 0 remaining
Thread D acquires: BLOCKED  <- waits

Thread A releases: [■ □ □]  <- Thread D wakes, acquires
```

A binary semaphore (N=1) allows one thread. Similar to a mutex, but with a critical difference: mutexes must be released by the acquiring thread. Semaphores can be released by any thread.

This enables signaling patterns impossible with mutexes:

```rust
// Producer-consumer signaling
// Producer thread:
data_ready_semaphore.release();  // Signal data available

// Consumer thread:
data_ready_semaphore.acquire();  // Wait for signal
process(data);
```

The producer releases a permit it never acquired. Natural with semaphores, impossible with mutexes.

### Mutex vs Semaphore

Mutexes protect data. Semaphores control access to a count of resources.

| Use Case | Primitive |
|----------|-----------|
| Protect shared HashMap | Mutex |
| Limit concurrent DB connections to 20 | Semaphore |
| Single-thread config modification | Mutex |
| Throttle API requests to 100/sec | Semaphore |

Use mutexes for mutual exclusion. Use semaphores to limit concurrency to N.

## Tokio's Semaphore

Production async Rust uses Tokio's Semaphore.

```rust
use tokio::sync::Semaphore;
use std::sync::Arc;

const MAX_CONCURRENT: usize = 10;

#[tokio::main]
async fn main() {
    let semaphore = Arc::new(Semaphore::new(MAX_CONCURRENT));
    let urls: Vec<String> = get_urls();

    let handles: Vec<_> = urls.into_iter().map(|url| {
        let sem = Arc::clone(&semaphore);
        tokio::spawn(async move {
            let _permit = sem.acquire().await.unwrap();
            let response = reqwest::get(&url).await;
            // permit dropped, automatically released
        })
    }).collect();

    futures::future::join_all(handles).await;
}
```

**Arc<Semaphore>** - Shared across spawned tasks. Each task clones the Arc, not the semaphore.

**acquire().await** - Returns SemaphorePermit. Async-aware: yields instead of blocking the thread when no permits available.

**RAII permits** - Permit bound to _permit. When out of scope, Drop runs and permit returns to pool. No manual release() needed. Resource cleanup that cannot be forgotten.

### Acquisition Failure

acquire() returns Result<SemaphorePermit, AcquireError>. Fails if semaphore is closed:

```rust
let sem = Semaphore::new(5);
sem.close(); // No more permits issued
sem.acquire().await; // Returns Err(AcquireError)
```

Closing signals shutdown. All waiters notified that the resource is unavailable.

### Non-Blocking Acquisition

Acquire only if permit immediately available:

```rust
match semaphore.try_acquire() {
    Ok(permit) => {
        // Got permit, do work
    }
    Err(_) => {
        // No permits, do something else
    }
}
```

### Multiple Permits

For weighted resources where operations have different costs:

```rust
// Acquire 3 permits at once
let _permit = semaphore.acquire_many(3).await.unwrap();
```

Blocks until 3 permits available simultaneously. Bulk database write might need more permits than single-row read.

## Building from Scratch

Implementation using std::sync:

```rust
use std::sync::{Mutex, Condvar};

pub struct Semaphore {
    state: Mutex<usize>,
    cond: Condvar,
}

impl Semaphore {
    pub fn new(permits: usize) -> Self {
        Self {
            state: Mutex::new(permits),
            cond: Condvar::new(),
        }
    }

    pub fn acquire(&self) {
        let mut count = self.state.lock().unwrap();
        while *count == 0 {
            count = self.cond.wait(count).unwrap();
        }
        *count -= 1;
    }

    pub fn release(&self) {
        let mut count = self.state.lock().unwrap();
        *count += 1;
        self.cond.notify_one();
    }
}
```

Working semaphore in 25 lines.

### Condvar Pattern

Condvar (condition variable) lets a thread sleep until a condition becomes true:

1. Lock the mutex
2. Check condition (permits > 0?)
3. If false, wait() atomically releases lock and sleeps
4. When woken, lock reacquired and loop repeats

More efficient than spinning. Thread yields to OS scheduler instead of burning CPU.

### The While Loop

```rust
while *count == 0 {
    count = self.cond.wait(count).unwrap();
}
```

Must be while, not if. Two reasons:

**Spurious wakeups** - OS can wake thread for reasons unrelated to notify_one(). Rare but happens. Loop re-checks condition.

**Multiple waiters** - If three threads wait and one permit released, notify_one() wakes one thread. By the time it acquires the lock, another thread might have grabbed the permit. Loop handles this race.

### RAII Permits

Manual release() calls are error-prone. Add a guard:

```rust
pub struct SemaphorePermit<'a> {
    sem: &'a Semaphore,
}

impl Drop for SemaphorePermit<'_> {
    fn drop(&mut self) {
        self.sem.release();
    }
}

impl Semaphore {
    pub fn acquire_permit(&self) -> SemaphorePermit<'_> {
        self.acquire();
        SemaphorePermit { sem: self }
    }
}
```

Permits release automatically when out of scope:

```rust
{
    let _permit = semaphore.acquire_permit();
    do_work();
} // permit released here, even if do_work() panics
```

Lifetime 'a ties permit to semaphore. Cannot outlive it. Borrow checker enforces correct usage at compile time.

### Panic Safety

Thread panics while holding permit? RAII guard Drop still runs during stack unwinding. Permit released. No leaked resources.

If acquire() panics mid-operation? Mutex becomes poisoned. Subsequent lock() calls return Err. Rust signals protected state might be inconsistent. For a semaphore, count is just an integer, so poisoning is overly conservative. Production code might use parking_lot::Mutex which does not poison.

## no_std Implementation

No operating system. No heap. No std::sync. Just hardware.

Embedded contexts cannot use Mutex and Condvar. They rely on OS primitives that do not exist. But core::sync::atomic is available everywhere. Build a semaphore with atomics.

```rust
#![no_std]

use core::sync::atomic::{AtomicUsize, Ordering};
use core::hint::spin_loop;

pub struct SpinSemaphore {
    permits: AtomicUsize,
}

impl SpinSemaphore {
    pub const fn new(permits: usize) -> Self {
        Self {
            permits: AtomicUsize::new(permits),
        }
    }

    pub fn acquire(&self) {
        loop {
            let current = self.permits.load(Ordering::Acquire);
            if current > 0 {
                if self.permits
                    .compare_exchange_weak(
                        current,
                        current - 1,
                        Ordering::AcqRel,
                        Ordering::Acquire,
                    )
                    .is_ok()
                {
                    return;
                }
            }
            spin_loop();
        }
    }

    pub fn release(&self) {
        self.permits.fetch_add(1, Ordering::Release);
    }
}
```

**compare_exchange_weak** - Atomically: if value still current, swap to current - 1. If another thread modified count between load and this call, fail and retry.

**spin_loop()** - Hints to CPU we are in busy-wait loop. On x86 emits PAUSE, reducing power consumption and avoiding pipeline stalls.

**const fn new()** - Constructor runs at compile time. Enables static semaphores:

```rust
static SENSOR_LOCK: SpinSemaphore = SpinSemaphore::new(1);
```

### Memory Ordering

Orderings matter:

- Ordering::Acquire on load - See all writes from before last release()
- Ordering::Release on store - Writes visible to next acquire()
- Ordering::AcqRel on compare_exchange - Both acquire and release semantics

These prevent compiler and CPU from reordering operations in ways that break synchronization.

### Tradeoffs

Spin-locks burn CPU while waiting. Fine for:
- Short critical sections
- Real-time systems where blocking is worse than spinning
- Single-core embedded where nothing else to schedule

Bad for:
- Long waits
- Battery-powered devices
- General application code

### Embassy for Async Embedded

Async in embedded uses Embassy semaphores that yield instead of spin:

```rust
use embassy_sync::semaphore::Semaphore;
use embassy_sync::blocking_mutex::raw::CriticalSectionRawMutex;

// Static semaphore, 3 permits, no heap allocation
static SEM: Semaphore<CriticalSectionRawMutex, 3> = Semaphore::new();

async fn do_work() {
    let _permit = SEM.acquire().await;
    // ...
}
```

Embassy executor handles waking. Tasks do not burn cycles waiting.

## Go Comparison

Infrastructure work involves both Rust and Go. Semaphore patterns transfer directly.

Go has no standard library semaphore. Lives in golang.org/x/sync:

```go
import (
    "context"
    "golang.org/x/sync/semaphore"
)

var sem = semaphore.NewWeighted(10)

func fetchWithLimit(ctx context.Context, url string) error {
    if err := sem.Acquire(ctx, 1); err != nil {
        return err // context cancelled
    }
    defer sem.Release(1)

    return fetch(url)
}
```

### Side-by-Side

Connection pool pattern in both languages:

**Go:**
```go
var dbSem = semaphore.NewWeighted(20)

func queryDB(ctx context.Context, sql string) (Result, error) {
    if err := dbSem.Acquire(ctx, 1); err != nil {
        return nil, err
    }
    defer dbSem.Release(1)

    return db.Query(sql)
}
```

**Rust:**
```rust
static DB_SEM: Lazy<Semaphore> = Lazy::new(|| Semaphore::new(20));

async fn query_db(sql: &str) -> Result<QueryResult, Error> {
    let _permit = DB_SEM.acquire().await?;
    db.query(sql).await
}
```

### Key Differences

| | Rust (tokio) | Go (x/sync) |
|--|--------------|-------------|
| Cleanup | RAII - drop releases | Manual - defer required |
| Cancellation | tokio::select! | context.Context |
| Weighted acquire | acquire_many(n) | Acquire(ctx, n) |
| Try acquire | try_acquire() | TryAcquire(n) |
| Zero-cost static | No (Lazy or OnceLock) | No (init at runtime) |

Biggest difference is cleanup. Rust permit drops automatically. Go requires defer. Forget it, leak permits. Production issues from missing defer caused gradual resource exhaustion.

Go's context.Context is more pervasive than Rust cancellation patterns. Every blocking Go function takes context. In Rust, use tokio::select! with cancellation token:

```rust
tokio::select! {
    permit = sem.acquire() => { /* got it */ }
    _ = shutdown_signal.recv() => { /* cancelled */ }
}
```

Different idioms, same result.

## Performance Considerations

Production load reveals bottlenecks.

**Contention** - Many threads competing for few permits creates bottleneck. Mutex inside gets hammered. Lock contention in profiles suggests sharding: multiple semaphores, each guarding resource subset.

**Fairness** - Condvar implementation has no guaranteed wake order. Thread A might wait longer than Thread B, even if A arrived first. Tokio semaphore is mostly FIFO. Spin-locks are completely unfair - whoever wins CAS race gets permit. Most applications do not care. Latency-sensitive systems might.

**Cache-line bouncing** - Every atomic operation on permit count invalidates cache line across all cores. Under high contention, cores spend time synchronizing caches instead of doing work. Unavoidable with shared counters. Reduce frequency by batching (acquire multiple permits at once) or redesigning to reduce contention.

**When to use something else:**
- Semaphore as work queue: channel might be clearer
- Permits map 1:1 to pooled objects: dedicated pool (deadpool, bb8) handles bookkeeping
- Rate limiting by time: token bucket or leaky bucket more precise

Semaphores are good default. When performance matters, profile first, then decide if abstraction fits.

## Conclusion

Semaphores are foundational for async programming. They appear in bounded concurrency: connection pools, rate limiters, worker queues, backpressure mechanisms.

Three implementations covered:
- Tokio semaphore for async Rust production
- Mutex + Condvar to understand internals
- no_std spin-lock version for embedded

The mental model is consistent: N permits, acquire blocks at zero, release wakes a waiter. Same pattern applies across languages and runtimes.

Further exploration: implement fair semaphore (FIFO wakeup order), add timeout support, benchmark spin-lock version against parking_lot. The primitives are small enough to understand completely but rich enough for continued learning.

---
