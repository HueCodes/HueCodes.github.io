---
layout: post
title: "Sensor-Bridge: Lock-Free Pipeline for Robotics"
date: 2026-01-31
category: projects
---

High-throughput sensor processing pipeline with lock-free SPSC buffers and sub-microsecond latency.

![Pipeline Architecture](/assets/images/projects/sensor-bridge-pipeline.png)

Robotics sensors produce data at high rates. An IMU outputs 1000 samples/second. A LiDAR pushes 300K points/second. This data needs filtering, fusion, and forwarding—without blocking the sensor or dropping samples.

Sensor-Bridge is a 4-stage pipeline optimized for this workload. Each stage runs on its own thread. Stages communicate through lock-free ring buffers. No mutexes, no syscalls, no jitter.

## Pipeline Stages

**Ingestion**: Receives raw sensor data. Timestamps arrivals, handles protocol parsing.

**Filtering**: Validates and transforms. Outlier rejection, unit conversion, coordinate transforms.

**Aggregation**: Combines multiple inputs. Running statistics, windowed averages, sensor fusion.

**Output**: Final delivery. Forwarding to control loops, logging, network transmission.

Each stage is a trait. Plug in your own processing logic. The pipeline handles threading and data flow.

## Lock-Free Design

The SPSC (single-producer, single-consumer) ring buffer is the core primitive. One thread writes, one thread reads, no locks needed.

Implementation details:
- Power-of-two buffer size for fast modulo (bitwise AND)
- Cache-line padding between head/tail pointers (prevents false sharing)
- Acquire-release memory ordering (minimal synchronization)

Why lock-free? Mutexes cause priority inversion. A high-priority thread blocks waiting for a low-priority thread holding the lock. In real-time systems, this means missed deadlines. Lock-free structures eliminate this class of bugs.

Debugging lock-free code is painful. Race conditions are intermittent. The fix: extensive stress testing with ThreadSanitizer, plus formal reasoning about memory ordering.

Memory ordering took multiple iterations to get right. Relaxed ordering is fast but wrong. SeqCst is correct but slow. Acquire-release hits the sweet spot for SPSC.

## Backpressure Handling

When a downstream stage can't keep up, the buffer fills. Three strategies:

**Block**: Producer waits for space. Simple, preserves all data, but propagates slowdowns upstream.

**Drop oldest**: Overwrite stale data. Good for sensors where only recent values matter.

**Drop newest**: Reject incoming data. Preserves historical data at the cost of gaps.

The choice depends on your application. IMU data for state estimation needs continuity—drop oldest. Event streams might need drop newest to avoid stale processing.

## Performance

| Metric | Result |
|--------|--------|
| Pipeline throughput | >1M items/sec |
| Stage processing | 2.2B items/sec |
| Channel latency | ~20ns |

Benchmarking required care. Measuring nanosecond operations means cache effects dominate. Warm-up runs, pinned cores, and isolated CPUs were necessary for stable numbers.

SPSC limits you to one producer and one consumer. An MPMC (multi-producer, multi-consumer) variant would enable fan-in/fan-out patterns. The tradeoff is complexity—MPMC lock-free queues are significantly harder to implement correctly.

I'd also add built-in metrics. Right now users instrument manually. Integrating latency histograms per stage would help debugging.

---

[View on GitHub](https://github.com/HueCodes/Sensor-Bridge)
