---
layout: post
title: "Understanding Semaphores in Rust"
date: 2026-02-03
category: posts
---

Synchronization primitive for controlling concurrent access to shared resources.

Rust's standard library provides Mutex, RwLock, Condvar, channels, and atomics. No semaphore. This is intentional. Tokio provides tokio::sync::Semaphore. The parking_lot crate offers alternatives. Understanding the mechanism helps when building concurrent systems.

Semaphores appear in production systems. Connection pools, API rate limiting, worker thread bounds. Any system limiting concurrent resource access uses this pattern.

```rust
// Connection pool is a semaphore
let pool = Pool::new(max_connections);
let conn = pool.get().await; // blocks if pool exhausted
```

## How Semaphores Work

A semaphore maintains a count of available permits.

**Acquire** - Decrement the count. If zero, block until available.

**Release** - Increment the count. Wake a waiting thread.

A counting semaphore allows N concurrent accessors. Initialize with 10 permits, 10 threads can hold the resource. The 11th blocks. A binary semaphore (N=1) allows one thread. Similar to a mutex, but semaphores can be released by any thread, not just the one that acquired it.

This enables signaling patterns:

```rust
// Producer-consumer signaling
// Producer:
data_ready.release();  // Signal available

// Consumer:
data_ready.acquire();  // Wait for signal
process(data);
```

The producer releases a permit it never acquired.

### When to Use Each

Mutexes protect data. Semaphores control resource count.

| Use Case | Primitive |
|----------|-----------|
| Protect shared HashMap | Mutex |
| Limit concurrent DB connections | Semaphore |
| Throttle API requests | Semaphore |

## Tokio's Semaphore

Production async Rust:

```rust
use tokio::sync::Semaphore;
use std::sync::Arc;

#[tokio::main]
async fn main() {
    let semaphore = Arc::new(Semaphore::new(10));
    let urls: Vec<String> = get_urls();

    let handles: Vec<_> = urls.into_iter().map(|url| {
        let sem = Arc::clone(&semaphore);
        tokio::spawn(async move {
            let _permit = sem.acquire().await.unwrap();
            reqwest::get(&url).await
            // permit auto-released when dropped
        })
    }).collect();

    futures::future::join_all(handles).await;
}
```

Permits are RAII. When they go out of scope, the resource returns to the pool automatically. No manual cleanup needed.

Try acquire without blocking:

```rust
match semaphore.try_acquire() {
    Ok(permit) => { /* do work */ }
    Err(_) => { /* no permits available */ }
}
```

Weighted operations for varying costs:

```rust
let _permit = semaphore.acquire_many(3).await.unwrap();
```

## Building from Scratch

Simple implementation using std::sync:

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

Condvar lets threads sleep until a condition becomes true. More efficient than spinning. The while loop handles spurious wakeups and multiple waiters racing for the same permit.

Add RAII cleanup:

```rust
pub struct SemaphorePermit<'a> {
    sem: &'a Semaphore,
}

impl Drop for SemaphorePermit<'_> {
    fn drop(&mut self) {
        self.sem.release();
    }
}
```

Permits release automatically, even if the code panics.

## Embedded Implementation

For no_std environments without OS primitives:

```rust
use core::sync::atomic::{AtomicUsize, Ordering};

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
                    .compare_exchange_weak(current, current - 1,
                        Ordering::AcqRel, Ordering::Acquire)
                    .is_ok()
                {
                    return;
                }
            }
            core::hint::spin_loop();
        }
    }

    pub fn release(&self) {
        self.permits.fetch_add(1, Ordering::Release);
    }
}
```

Spin-locks work for short waits in embedded contexts. Bad for battery-powered devices or long operations.

## Go Comparison

Go has semaphores in golang.org/x/sync:

```go
var sem = semaphore.NewWeighted(10)

func fetchWithLimit(ctx context.Context, url string) error {
    if err := sem.Acquire(ctx, 1); err != nil {
        return err
    }
    defer sem.Release(1)
    return fetch(url)
}
```

Main difference is cleanup. Rust permits drop automatically. Go requires defer. Forget it and you leak permits.

## Conclusion

Semaphores control bounded concurrency. Connection pools, rate limiters, worker queues all use this pattern.

Core concept: N permits, acquire blocks at zero, release wakes waiters. Same across languages and runtimes. Use Tokio for async production code. Build from Mutex + Condvar to understand internals. Use atomics for embedded contexts.

---
