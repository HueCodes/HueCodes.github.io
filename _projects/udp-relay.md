---
layout: post
title: "UDP-Relay: MAVLink v2 Telemetry Aggregator"
---

High-throughput telemetry aggregator for drone fleets. Ingests MAVLink v2 UDP packets, validates frames, maintains a thread-safe drone registry, and streams state to WebSocket clients via a non-blocking pub/sub hub.

![Architecture](/assets/images/projects/udp-relay-arch.svg)

## MAVLink Parsing

The parser handles MAVLink v2 frames exclusively. Each frame starts with the `0xFD` magic byte, followed by a 9-byte header, variable-length payload, and a CRC-16/MCRF4XX checksum. CRC seeds are hardcoded per message ID — HEARTBEAT uses seed 50, GPS_RAW_INT uses 24, ATTITUDE uses 39. The parser validates system IDs (1-250 only), rejects malformed payloads, and uses zero-copy design where payload slices reference the original buffer rather than allocating new memory.

Six message types are decoded: HEARTBEAT (armed state, flight mode, autopilot type), GPS_RAW_INT (lat/lon/alt with fix quality), ATTITUDE (roll/pitch/yaw with angular velocities), GLOBAL_POSITION_INT (global position with ground speed), BATTERY_STATUS (cell voltages, current, remaining percentage), and EXTENDED_SYS_STATE. GPS coordinates are validated by range and fix type. Battery values are bounds-checked. Flight modes are decoded from PX4's custom mode bitfield — bits 16-23 for main mode, 24-31 for sub mode.

## Drone Registry

The registry is a `map[DroneID]*State` protected by an RWMutex. Each drone's state tracks position, attitude, battery, heartbeat, connection status, flight mode, and message count. State snapshots use shallow copies with pointer fields for cheap cloning — the WebSocket broadcaster can serve state without holding locks. A background goroutine runs every 10 seconds and marks drones disconnected after 30 seconds without a heartbeat.

`GetAllSummaries()` returns lightweight summary structs with only the fields WebSocket clients need: system ID, armed status, flight mode, lat/lon/alt/heading as optional pointers, battery percentage, and vehicle type. This keeps broadcast payloads small.

## Rate Limiting and Ingestion

Per-drone token bucket rate limiting prevents one noisy vehicle from starving others. Each system ID gets its own bucket with a 200 tokens/second refill rate and 50-token burst capacity, lazily initialized on first packet. The rate limit check happens after parsing but before the output channel send — invalid frames are rejected before consuming rate limit tokens.

The UDP listener binds to port 14550 with an 8 MB socket buffer. Packets queue into a 10,000-slot channel consumed by 8 worker goroutines. Each worker decodes the MAVLink frame, checks the rate limit, and sends events to the pub/sub hub. Workers recover from panics and restart automatically. Optional source IP whitelisting accepts CIDR ranges and drops packets from unlisted addresses.

## Pub/Sub and WebSocket Streaming

The hub implements single-input, multi-output fan-out. Events from the worker pool broadcast to all subscribers through 256-entry buffered channels. Slow subscribers get dropped events rather than back-pressuring the ingest pipeline — counted in Prometheus metrics.

WebSocket clients connect and optionally send subscription filters to watch specific drone IDs. The broadcaster accumulates events for 100ms intervals, snapshots all drone summaries, filters per client, and sends batched JSON updates. Per-client write timeouts (10s) and periodic pings (30s) detect stale connections. Maximum concurrent clients defaults to 100.

## Observability

Prometheus metrics cover the full pipeline: packets received/dropped, parse errors, active drones, WebSocket client count, event channel utilization, worker pool utilization, rate-limited packets, and telemetry latency histograms. The health endpoint reports per-component status including goroutine count and heap allocation. Graceful shutdown on SIGINT/SIGTERM drains the worker queue, flushes remaining events, closes WebSocket connections, and prints final statistics.

---

[View on GitHub](https://github.com/HueCodes/udp-relay)
