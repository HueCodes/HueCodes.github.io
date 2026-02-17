---
layout: hardware
title: LoRaWAN Mesh Network
status: in progress
github:
---

## Overview

Long-range mesh network using LoRaWAN for distributed sensor deployment across agricultural environments. Designed for low-power, wide-area coverage with multi-hop routing.

## Why LoRaWAN for Agriculture?

LoRaWAN provides:
- **Range:** 1-10km line of sight (perfect for farms/orchards)
- **Low power:** Sensors run for years on batteries
- **Mesh capability:** Multi-hop routing for coverage extension
- **Unlicensed spectrum:** No ongoing RF licensing costs

This is the standard wireless protocol for farm-scale IoT sensor networks.

## Hardware

| Component | Description |
|-----------|-------------|
| LoRa modules | SX1276/SX1278 transceivers |
| Microcontroller | ESP32 or STM32 |
| Antenna | 915MHz (US) or 868MHz (EU) |
| Power | Solar + LiPo battery for outdoor deployment |

## Architecture

```
[Sensor Node] --LoRa--> [Gateway Node] --LoRa--> [Base Station]
                              |
                         [Sensor Node]
```

Multi-hop mesh allows sensors beyond gateway range to relay through intermediate nodes.

## Use Cases

- **Orchard monitoring:** Soil moisture, temperature, humidity sensors across large orchards
- **Environmental sensing:** Weather stations distributed across farmland
- **Asset tracking:** Equipment and livestock location tracking

## Technical Details

- **Frequency:** 915MHz (US ISM band)
- **Data rate:** 0.3-50 kbps (configurable)
- **Range:** Up to 10km line-of-sight, 2-3km with obstacles
- **Power:** <50mA transmit, <15mA receive, <1µA sleep

## Build Log

### Current Progress

*Fill in as you build:*
- Initial hardware testing
- Range testing results
- Mesh routing implementation
- Power consumption measurements
- Field deployment results

## What I'm Learning

*Document challenges, insights, and solutions as you work through the project.*

## Relevance to Agricultural Robotics

Direct application to systems like Orchard Robotics' tractor-mounted camera networks:
- Wireless communication across large orchards
- Low-power sensor networks for environmental monitoring
- Mesh networking for reliability in challenging RF environments
