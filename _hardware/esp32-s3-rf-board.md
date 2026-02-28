---
layout: hardware
title: ESP32-S3 RF Development Board
status: in progress - v1.1 layout complete, pending fabrication
github:
---

![ESP32-S3 RF Board layout](/assets/images/projects/esp32-s3-rf-board.jpg)

4-layer RF development board using the bare **ESP32-S3 QFN56** die (not a module) in KiCAD 8. **50 x 40 mm**, JLC04161H-7628 stackup.

## Components

| Ref | Part | Notes |
|-----|------|-------|
| U1 | ESP32-S3 QFN56 | Dual-core LX7, 2.4GHz WiFi/BT5 |
| U2 | AP2112K-3.3V | 600mA LDO |
| U3 | CP2102-GMR | USB-UART |
| ANT1 | Johanson 2450AT18A100 | 2.4GHz chip antenna |
| J2 | u.FL SMD | RF test port |

## Stackup

| Layer | Function |
|-------|----------|
| L1 F.Cu | Signals, RF trace |
| Prepreg | 0.2mm, Er = 4.4 |
| L2 In1.Cu | Solid GND |
| Core | FR4, 1.065mm |
| L3 In2.Cu | 3.3V plane |
| L4 B.Cu | Secondary signals |

## RF Design

RF trace from ESP32-S3 RF pin to ANT1: **50 ohm microstrip**, 0.6mm width (IPC-2141A, h=0.2mm, Er=4.4). All bends 45 degrees.

Antenna keepout: **5mm radius on F.Cu, In1.Cu, B.Cu**.

GND stitching vias along RF trace at **6mm spacing** (lambda/10 at 2.4GHz).

## Power

AP2112K-3.3V, USB 5V in. ESP32-S3 peaks ~350mA during WiFi TX. 10uF bulk caps on LDO in/out, 100nF decoupling on each VCC pin.

## Bring-up Firmware

ESP-IDF v5. WiFi scan every 10 seconds over UART at 115200 to verify RF path. GPIO48 status LED at 1Hz. Source in `firmware/`.
