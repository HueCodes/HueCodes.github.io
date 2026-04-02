---
layout: hardware
title: Raft Consensus on Microcontrollers
status: in progress
github:
---

Distributed consensus protocol running on a **5-node ESP32-S3 cluster** communicating over ESP-NOW. Leader election, log replication, and fault tolerance -- on real hardware. Kill any 2 nodes and the cluster keeps running. Bring them back and they catch up automatically.

## Architecture

```
+------------------+     ESP-NOW (250B frames, 1-10ms latency)     +------------------+
|   Node 0         |<---------------------------------------------->|   Node 1         |
|   ESP32-S3       |         broadcast heartbeats                   |   ESP32-S3       |
|   BME280 (I2C)   |         unicast vote responses                 |   BME280 (I2C)   |
|   WS2812 LED     |         unicast AppendEntries replies          |   WS2812 LED     |
|   OLED SSD1306   |                                                |   OLED SSD1306   |
|   SPI FRAM       |                                                |   SPI FRAM       |
+------------------+                                                +------------------+
```

Every node is identical hardware and firmware. Node ID is set via NVS config at flash time. 5 nodes total in full mesh.

## Raft Protocol

Each node is always Follower, Candidate, or Leader. Two core RPCs:

**RequestVote** -- a candidate solicits votes after its election timeout fires. A node votes for at most one candidate per term, and only if the candidate's log is at least as up-to-date.

**AppendEntries** -- the leader replicates log entries and sends heartbeats (empty entries). Followers reject if their log doesn't match at `prevLogIndex`.

## Persistent State

Stored in SPI FRAM (MB85RC256V) for unlimited write endurance:

| Field | Size | Notes |
|-------|------|-------|
| `currentTerm` | 4 bytes | Latest term seen |
| `votedFor` | 1 byte | Vote in current term (0xFF = none) |
| `logEntryCount` | 4 bytes | Number of committed entries |

Log entries stored in SPI Flash (W25Q32) with circular buffer and wear-leveling.

## Timing

| Parameter | Value | Rationale |
|-----------|-------|-----------|
| Heartbeat | 200 ms | ~20x ESP-NOW RTT |
| Election timeout | 1000-2000 ms | Randomized to prevent split votes |
| RPC timeout | 100 ms | |
| Snapshot threshold | 100 entries | Triggers InstallSnapshot for lagging nodes |

## Application Layer

The replicated state machine drives three features:

- **Sensor fusion** -- median temperature/humidity from BME280 across all nodes, with outlier rejection
- **Synchronized LEDs** -- WS2812B RGB state replicated via the Raft log, all nodes display the same color
- **Key-value store** -- serial console CLI with SET/GET/GETL commands, plus cluster STATUS and fault injection (KILL/REVIVE)

## Status

- ESP-NOW transport and node identity complete
- Leader election and heartbeats in progress
- Log replication next
