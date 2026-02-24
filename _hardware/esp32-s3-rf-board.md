---
layout: hardware
title: ESP32-S3 RF Development Board
status: in progress - v1.1 layout complete, pending fabrication
github:
---

![ESP32-S3 RF Board layout](/assets/images/projects/esp32-s3-rf-board.jpg)

## Overview

A 4-layer ESP32-S3 development board designed from scratch using the bare die — not a module. The goal is full control over the RF path, power architecture, and layout, producing a clean portfolio-quality board that demonstrates real RF and mixed-signal PCB design.

The board targets 2.4GHz dual-mode WiFi and Bluetooth, with a 50-ohm controlled-impedance feedline from the ESP32-S3 RF pin to a Johanson chip antenna, plus a u.FL connector for VNA testing.

## Why a bare chip instead of a module?

Using a pre-certified module like the ESP32-S3-WROOM abstracts away all the RF work. A bare die requires designing the antenna feedline, setting up impedance-controlled traces, managing the ground plane, and understanding the electrical constraints that make wireless actually work. That's the point.

| Approach | RF knowledge required | Portfolio value |
|----------|----------------------|-----------------|
| Module (e.g. WROOM) | Minimal | Low |
| Bare chip (this board) | Substantial | High |

## Board Specifications

| Parameter | Value |
|-----------|-------|
| Dimensions | 50mm × 40mm |
| Layers | 4 (JLC04161H-7628 stackup) |
| Manufacturer | JLCPCB |
| MCU | ESP32-S3 QFN56 bare die |
| Wireless | 2.4GHz WiFi 802.11b/g/n + Bluetooth 5.0 |
| Antenna | Johanson 2450AT18A100 chip antenna |
| RF test port | u.FL connector |
| Power input | USB-C (5V) |
| Power regulation | AP2112K-3.3V LDO (600mA) |
| USB bridge | CP2102 USB-to-UART |

## Layer Stackup

| Layer | Role | Constraint |
|-------|------|-----------|
| L1 (F.Cu) | RF feedline, components, signals | 50Ω microstrip: 0.6mm trace over 0.2mm 7628 prepreg |
| L2 (In1.Cu) | Solid GND plane | No traces, no cuts — unbroken reference plane |
| L3 (In2.Cu) | 3.3V power distribution | Poured zone covering most of board |
| L4 (B.Cu) | Secondary signals, passives | |

The 0.2mm prepreg thickness between L1 and L2 (with Er = 4.4) gives a 50-ohm characteristic impedance at 0.6mm trace width — verified against JLCPCB's impedance calculator.

## RF Design

The RF path from ESP32-S3 to antenna is the most critical part of the board:

- **Feedline:** 0.6mm microstrip on F.Cu, 0.6mm trace width (50Ω, ±10%)
- **Routing:** 45-degree bends only, no 90-degree corners
- **Stitching vias:** GND vias every 6mm along both sides of the feedline (lambda/10 at 2.4GHz in FR4 ≈ 60mm)
- **Keepout zone:** 5mm copper-free radius around the antenna radiating element on all 4 layers
- **Ground plane:** Solid In1.Cu pour with 9-via grid under the ESP32-S3 exposed thermal pad

## Peripherals

| Interface | Connector | Pins |
|-----------|-----------|------|
| UART debug | 4-pin 2.54mm header | VCC, GND, TX, RX |
| I2C | 4-pin 2.54mm header | VCC, GND, SDA, SCL |
| SPI | 6-pin 2.54mm header | VCC, GND, MOSI, MISO, SCK, CS |
| USB | USB-C receptacle | Power + serial via CP2102 |
| Reset | Tactile button | EN pin |
| Boot | Tactile button | GPIO0 |
| Status LED | 0402 red + 330Ω | GPIO48 |
| Power LED | 0402 green + 1kΩ | 3.3V rail |

## Bill of Materials

All components sourced from JLCPCB Basic Parts Library where possible.

| Reference | Value | JLCPCB Part | Notes |
|-----------|-------|-------------|-------|
| U1 | ESP32-S3 QFN56 | C2913202 | Bare die, not module |
| U2 | AP2112K-3.3TRG1 | C51118 | 600mA LDO |
| U3 | CP2102-GMR | C6568 | USB-to-UART bridge |
| ANT1 | Johanson 2450AT18A100 | C167687 | 2.4GHz chip antenna |
| J2 | u.FL/IPEX SMD | C88374 | RF test connector |
| J1 | USB-C 16P | C165948 | Power + data |
| C1–C6, C9–C11 | 100nF 0402 | C1525 | Decoupling, per VCC pin |
| C7, C8 | 10uF 0805 | C17024 | LDO bulk input/output |
| R1 | 330Ω 0402 | C23197 | Status LED current limit |
| R2 | 1kΩ 0402 | C11702 | Power LED current limit |

## Build Log

### v1.1 layout — Feb 2026

The board is divided into three zones.

**RF section (right side).** The ESP32-S3 RF pin connects to the Johanson 2450AT18A100 via a 0.6mm 50-ohm microstrip on F.Cu. Bends are 45 degrees only. GND stitching vias run at 6mm intervals along both sides of the feedline. A 5mm copper-free keepout zone covers the antenna on all four layers. The u.FL connector taps into the RF net for VNA access.

**Power section (bottom left).** USB-C feeds 5V to the AP2112K LDO over a short trace. The LDO outputs 3.3V to the ESP32-S3, CP2102, and headers. 10uF bulk caps sit at the LDO input and output. The CP2102 is placed between USB-C and the ESP32-S3, keeping the D+/D- pair short.

**Debug peripherals (left edge).** UART, I2C, and SPI headers stack vertically. All header signals route on B.Cu with via transitions at each end. This keeps F.Cu clear outside the RF path. Reset and boot buttons connect to EN and GPIO0 on the left side of the ESP32-S3. Status and power LEDs with current-limiting resistors sit along the bottom edge.

Next steps: run DRC, fix any clearance violations, export Gerbers, order from JLCPCB.

## What I'm Learning

RF design on a 4-layer board requires understanding the physics behind every design decision. Some of the more interesting constraints this project forced me to engage with:

- **Microstrip impedance** depends on the dielectric height to the reference plane — moving the LDO away from the USB-C connector was necessary to keep the 5V trace short, but the more interesting constraint is that every trace on F.Cu uses In1.Cu as its return path, which is why that plane must stay completely unbroken
- **GND stitching vias** exist because at 2.4GHz the wavelength in FR4 is ~60mm — vias spaced at lambda/10 (6mm) prevent the ground reference from developing resonant behavior
- **Decoupling capacitor placement** is about inductance, not just capacitance — the via connecting the cap's GND pad to the ground plane must be within 0.5mm of the pad, otherwise the parasitic inductance defeats the purpose at RF frequencies
- **Via-in-pad** for the ESP32-S3 exposed pad is technically ideal but requires ENIG finish; used a via grid just outside the pad edge instead for HASL compatibility
