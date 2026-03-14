---
layout: post
title: "Sensor-Bridge: Lock-Free Pipeline for Robotics"
---

High-throughput sensor processing pipeline for robotics. Lock-free SPSC buffers, sub-microsecond latency, `no_std` compatible for bare-metal targets.

![Pipeline Architecture](/assets/images/projects/sensor-bridge-pipeline.png)

Robotics sensors produce data at high rates. An IMU outputs 1000 samples/second. A LiDAR pushes 300K points/second. This data needs filtering, fusion, and forwarding without blocking the sensor or dropping samples.

Sensor-Bridge is a 4-stage pipeline optimized for this workload. Each stage runs on its own thread. Stages communicate through lock-free ring buffers. No mutexes, no syscalls, no jitter.

## Pipeline stages

**Ingestion**: Receives raw sensor data. Timestamps arrivals, handles protocol parsing.

**Filtering**: Validates and transforms. Built-in stages include moving average, exponential moving average, Kalman filter, median filter, low-pass, and high-pass filters. Bias correction, rotation matrices, and IMU unit conversion handle coordinate transforms.

**Aggregation**: Combines multiple sensor inputs. Timestamp-based synchronization aligns data from different sources. Weighted averaging and sensor fusion combine IMU, barometer, and LiDAR readings.

**Output**: Final delivery to control loops, logging, or network transmission.

Each stage is a trait. The `StageExt` trait and `Chain` combinator enable fluent composition: chain stages together with `.then()`, add transforms with `.map()`, filter data inline.

## Lock-free ring buffers

The SPSC (single-producer, single-consumer) ring buffer is the core primitive. One thread writes, one thread reads, no locks needed.

- Power-of-two buffer size for fast modulo (bitwise AND)
- Cache-line padding between head/tail pointers prevents false sharing
- Acquire-release memory ordering (minimal synchronization that's still correct)

Why lock-free? Mutexes cause priority inversion. A high-priority control loop blocks waiting for a low-priority logging thread holding the lock. In real-time systems, this means missed deadlines and unstable control. Lock-free structures eliminate this class of bugs.

Memory ordering took multiple iterations. Relaxed ordering is fast but wrong. SeqCst is correct but slow. Acquire-release is the sweet spot for SPSC.

## Zero-copy data flow

ObjectPool provides pre-allocated pools with RAII guards. BufferPool handles variable-size byte buffers. SharedData wraps data in Arc for zero-copy sharing between stages. Target: fewer than 10 allocations per 1000 items processed.

## Adaptive backpressure

When a downstream stage can't keep up, the buffer fills. Three strategies:

**Block**: Producer waits for space. Simple, preserves all data, propagates slowdowns upstream.

**Drop oldest**: Overwrite stale data. Good for sensors where only recent values matter (IMU state estimation).

**Drop newest**: Reject incoming data. Preserves historical data at the cost of gaps (event streams).

The AdaptiveController adds hysteresis with configurable high/low water marks. When utilization crosses the high mark, the pipeline degrades quality (lower sample rates, simpler filters). When it drops below the low mark, quality recovers. A token-bucket rate limiter prevents oscillation.

## Metrics

HDR latency histograms track p50, p95, and p99 per stage. Jitter tracking measures timing variance. A live terminal dashboard shows real-time metrics during development. CSV and JSON export support offline analysis.

## Performance

| Metric | Result |
|--------|--------|
| Pipeline throughput | >1M items/sec |
| Stage processing | 2.2B items/sec |
| Channel latency | ~20ns |
| Ring buffer push | 0.3ns |
| Ring buffer pop | 9ns |

Benchmarking nanosecond operations means cache effects dominate. Stable numbers required warm-up runs, pinned cores, and isolated CPUs.

## Limitations

SPSC is the default channel. MPMC via crossbeam is available but the core pipeline design assumes single-producer, single-consumer for maximum throughput. Fan-in/fan-out patterns work but require explicit wiring.

---

[View on GitHub](https://github.com/HueCodes/Sensor-Bridge)
