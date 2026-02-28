---
layout: hardware
title: LoRaWAN Mesh Network
status: in progress
github:
---

Long-range mesh network using LoRaWAN for distributed sensor deployment across agricultural environments. Low-power, wide-area coverage with multi-hop routing.

## Hardware

| Component | Description |
|-----------|-------------|
| Radio | SX1276 / SX1278 LoRa transceiver |
| MCU | ESP32 or STM32L4 |
| Antenna | 915MHz (US) or 868MHz (EU), 1/4-wave whip |
| Power | Solar panel + LiPo, or primary lithium AA cells |

## LoRa Physical Layer

LoRa uses chirp spread spectrum (CSS). The spreading factor (SF) controls the tradeoff between range, data rate, and airtime:

| SF | Data Rate | Sensitivity | Range (LoS) |
|----|-----------|-------------|-------------|
| SF7 | ~5.5 kbps | -123 dBm | 2-3 km |
| SF9 | ~1.8 kbps | -129 dBm | 5-7 km |
| SF12 | ~250 bps | -137 dBm | 10-15 km |

Link budget at SF12, BW125kHz: 14 dBm TX + 2 dBi antenna gain + 137 dBm Rx sensitivity = 153 dB. In practice obstacles, Fresnel zone clearance, and ground reflection eat 20-30 dB of that.

Agricultural deployments use SF9 as the default. SF12 is reserved for nodes at the edge of coverage. Higher SF means longer airtime, which burns battery faster and reduces network capacity.

## Node Architecture

Three node types:

**Sensor nodes** wake on a schedule, take a reading, transmit, and sleep. Target: <1µA sleep current, <50mA TX burst. With a 3.6V 3600mAh lithium AA cell and 10-minute reporting intervals, runtime exceeds two years.

**Relay nodes** run continuously and forward packets from sensor nodes that are out of direct gateway range. Multi-hop extends coverage without moving the gateway. A relay bridges two non-overlapping coverage zones.

**Gateway** aggregates traffic and bridges to TCP/IP (WiFi, Ethernet, or cellular). In large deployments a gateway covers 50-100 sensor nodes at SF9.

```
[Sensor] --LoRa--> [Gateway] --TCP--> [Server]
                       ^
[Sensor] --LoRa--> [Relay] --LoRa--> [Gateway]
```

## SX1276 Configuration

The SX1276 is controlled over SPI. Key registers for a basic TX:

```c
// Set LoRa mode, explicit header, SF9, BW125kHz, CR 4/5
write_reg(REG_MODEM_CONFIG1, 0x72);  // BW=125, CR=4/5, explicit
write_reg(REG_MODEM_CONFIG2, 0x94);  // SF=9, CRC on
write_reg(REG_MODEM_CONFIG3, 0x04);  // LNA AGC on

// Set frequency (915 MHz)
uint64_t frf = ((uint64_t)915000000 << 19) / 32000000;
write_reg(REG_FRF_MSB, (frf >> 16) & 0xFF);
write_reg(REG_FRF_MID, (frf >> 8) & 0xFF);
write_reg(REG_FRF_LSB, frf & 0xFF);

// TX power: 14 dBm via PA_BOOST
write_reg(REG_PA_CONFIG, 0x8C);
```

## Power Budget

Target: soil moisture + temperature node reporting every 10 minutes.

| State | Current | Duration | Energy |
|-------|---------|----------|--------|
| Sleep | 0.8 µA | 598s | 479 µAs |
| Wake + sensor read | 12 mA | 1s | 12 mAs |
| LoRa TX (SF9) | 45 mA | 0.18s | 8.1 mAs |
| Total per cycle | | 600s | ~500 µAs |

Average current: ~0.83 µA. A 3600mAh cell lasts roughly 5 years at this rate, before accounting for solar topping.

## Frequency and Regulatory Notes

US 915MHz ISM band: no duty cycle limit but EIRP capped at 30 dBm. EU 868MHz: 1% duty cycle per sub-band. At SF9 airtime per packet is ~180ms, giving a 1% duty cycle budget of 18 seconds per 30-minute window — plenty for 10-minute reporting.

## Status

- SX1276 SPI driver working, basic TX/RX confirmed
- Range testing underway, initial results ~4km LoS at SF9
- Multi-hop relay routing in progress
- Power profiling with PPK2 pending hardware delivery
