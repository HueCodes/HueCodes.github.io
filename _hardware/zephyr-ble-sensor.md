---
layout: hardware
title: Zephyr BLE Sensor Node
status: in progress
github:
---

## Overview

BLE peripheral on the nRF52840 DK using Zephyr RTOS. Reads a BME280 sensor, exposes data over a custom GATT service, and sleeps aggressively between readings. Demonstrates the Zephyr-specific stack (device tree, Kconfig, BLE GATT) used in IoT firmware roles.

## Hardware

| Component | Description |
|-----------|-------------|
| Board | Nordic nRF52840 DK (PCA10056) |
| Core | ARM Cortex-M4 @ 64MHz, BLE 5.0 |
| Memory | 1MB flash, 256KB RAM |
| Sensor | BME280 I2C (temp/humidity/pressure) |
| Power profiling | Nordic PPK2 (nA-resolution current measurement) |

## Architecture

```
BME280 (I2C)
    |
    v
[Sensor Thread] --> reads every N seconds --> [BLE GATT Notify]
                                                      |
                                            [Central / Phone App]

Between reads: PM sleep --> system off --> RTC wakeup
```

## Zephyr Concepts

### Device Tree

Hardware described in `.overlay` files rather than `#define` pin configs scattered in headers. The BME280 is declared as a node on the I2C bus:

```dts
/* boards/nrf52840dk_nrf52840.overlay */
&i2c0 {
    bme280@76 {
        compatible = "bosch,bme280";
        reg = <0x76>;
    };
};
```

At compile time Zephyr generates device handles. No manual I2C address init in `main()`.

### Kconfig

`prj.conf` selects subsystems at build time:

```
CONFIG_BT=y
CONFIG_BT_PERIPHERAL=y
CONFIG_BT_DEVICE_NAME="SensorNode"
CONFIG_SENSOR=y
CONFIG_BME280=y
CONFIG_PM=y
CONFIG_PM_DEVICE=y
```

### Custom GATT Service

Custom 128-bit UUID service with characteristics for temperature, humidity, and pressure. Notifies connected centrals on each new reading. Connection and disconnection callbacks control the advertising restart.

## Power Management

Zephyr PM subsystem puts the SoC into low-power states between readings. Sleep current profiled with the Nordic PPK2 — the goal is sub-10µA average at 1-reading-per-minute.

## Build

```bash
west build -b nrf52840dk_nrf52840 app
west flash
```
