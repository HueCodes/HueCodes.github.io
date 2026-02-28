---
layout: hardware
title: ESP32 Spectrum Analyzer
status: in progress
github:
---

Portable RF spectrum analyzer built around the ESP32. Real-time 2.4GHz spectrum visualization on an SSD1306 OLED. Useful for debugging LoRa, WiFi, and BLE RF environments.

## Hardware

| Component | Description |
|-----------|-------------|
| MCU | ESP32 (dual-core Xtensa LX6, 240MHz) |
| Display | SSD1306 128x64 OLED, I2C at 400kHz |
| Antenna | PCB trace + u.FL pigtail for external whip |
| Storage | SSD micro-SD via SPI for data logging |
| Power | 18650 LiPo, TP4056 charger, 3.3V LDO |

## How the ESP32 Scans the 2.4GHz Band

The ESP32 WiFi subsystem exposes a raw scan mode (`esp_wifi_set_promiscuous`) that puts the radio into a receive-only state per channel. By stepping through channels 1-13 and sampling RSSI from received frames, you build a per-channel power estimate.

This is not a true swept spectrum analyzer. It captures activity on discrete 20MHz-wide channels, not a continuous frequency sweep. For WiFi interference analysis and LoRa channel selection it is accurate enough.

```c
for (int ch = 1; ch <= 13; ch++) {
    esp_wifi_set_channel(ch, WIFI_SECOND_CHAN_NONE);
    vTaskDelay(pdMS_TO_TICKS(DWELL_MS));
    channel_rssi[ch] = read_average_rssi();
}
```

Dwell time per channel is 20ms. A full sweep takes ~260ms, giving roughly 4 sweeps per second on the display.

## Display

The SSD1306 is driven over I2C at 400kHz fast mode. The display buffer is 1024 bytes (128x64 / 8). A full redraw takes ~2.5ms at 400kHz. Each sweep maps channel RSSI to a bar height:

```c
uint8_t bar_height = (uint8_t)((rssi + 100) * 64 / 70);  // -100 to -30 dBm range
draw_bar(col, bar_height, SCREEN_HEIGHT);
```

The display shows:
- Channel number on the x-axis (1-13)
- RSSI in dBm on the y-axis (-100 to -30)
- Peak hold line in a dimmer shade
- Active WiFi networks listed by SSID below the spectrum

## What You Can See

**WiFi channel utilization.** Channels 1, 6, and 11 are non-overlapping. A crowded environment shows all three pegged. Moving a LoRa gateway's 2.4GHz WiFi backhaul to the least-loaded channel reduces interference.

**Bluetooth and BLE.** BLE uses adaptive frequency hopping across 40 channels at 2MHz spacing. It shows up as a broad raised floor across the entire band rather than a discrete peak.

**Microwave ovens.** Intermittent wide-band interference centered near 2.45GHz. Distinct pattern compared to WiFi or BLE.

**LoRa interference.** If a LoRa node is stuck transmitting (firmware bug, rogue device), the SX1276 at 2.4GHz variant shows up as a sharp narrow peak.

## SD Logging

Each sweep is written to the SD card as a CSV line with timestamp and per-channel RSSI:

```
1706745600,ch1:-72,ch2:-85,ch3:-78,ch4:-91,...,ch13:-88
```

Post-processing the logs identifies interference patterns over time, which is more useful than a live display for diagnosing intermittent problems.

## Status

- WiFi scan mode and OLED display working
- SD logging implemented
- Peak hold and waterfall display in progress
- Enclosure design pending
