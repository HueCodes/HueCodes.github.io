---
layout: hardware
title: LoRa Sensor Node
status: in progress
github:
---

Two-node raw LoRa P2P link for environmental sensing using **Heltec WiFi LoRa 32 V3** boards (ESP32-S3 + SX1262). Not LoRaWAN -- direct radio-to-radio communication with SPI-level debugging.

## Hardware

| Component | Qty | Description |
|-----------|-----|-------------|
| Heltec WiFi LoRa 32 V3 | 2 | ESP32-S3 + SX1262 + OLED, onboard u.FL |
| DHT11 | 1 | Temperature/humidity sensor |
| Logic analyzer | 1 | 24MHz 8CH for SPI captures |

## Radio Configuration

| Parameter | Value |
|-----------|-------|
| Frequency | 915 MHz (US ISM) |
| Spreading Factor | SF9 |
| Bandwidth | 125 kHz |
| Coding Rate | 4/7 |
| TX Power | 14 dBm |

Packet format: `T:23.4 H:61.2 N:0042` -- plain ASCII with sequence number for tracking packet loss.

## LoRa Physical Layer

LoRa uses chirp spread spectrum (CSS). Higher spreading factor = longer range but lower data rate:

| SF | Data Rate | Sensitivity | Range (LoS) |
|----|-----------|-------------|-------------|
| SF7 | ~5.5 kbps | -123 dBm | 2-3 km |
| SF9 | ~1.8 kbps | -129 dBm | 5-7 km |
| SF12 | ~250 bps | -137 dBm | 10-15 km |

SF9 is the default -- good balance of range and airtime for sensor reporting.

## Milestones

1. Environment setup and board validation
2. TX firmware -- periodic sensor read + LoRa transmit
3. RX firmware -- receive, parse, display on OLED
4. SPI captures -- logic analyzer traces of SX1262 register writes
5. Light sleep between transmissions
6. Current measurement and power profiling
7. Range testing outdoors
