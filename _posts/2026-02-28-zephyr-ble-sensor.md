---
layout: post
title: "Zephyr BLE Sensor on nRF52840: Device Tree, GATT, and PM Sleep"
date: 2026-02-28
category: projects
---

BLE peripheral on the nRF52840 DK using Zephyr RTOS. Reads a BME280 sensor, exposes data over a custom GATT service, and sleeps aggressively between readings. Built to learn the Zephyr-specific stack that shows up in IoT firmware job postings.

![Zephyr BLE architecture](/assets/images/projects/zephyr-ble-sensor.svg)

## The Device Tree Shift

The biggest mental model change coming from bare-metal or STM32CubeIDE is device tree. Hardware is declared in `.dts` and `.overlay` files, not in `#define` pin configs scattered through header files.

The BME280 is added to the nRF52840 DK's I2C bus via an overlay:

```dts
/* boards/nrf52840dk_nrf52840.overlay */
&i2c0 {
    bme280@76 {
        compatible = "bosch,bme280";
        reg = <0x76>;
    };
};
```

At build time Zephyr generates device handles from the tree. In application code you get the device using DTS macros, not by manually calling an I2C init function with a hardcoded address.

```c
const struct device *bme = DEVICE_DT_GET_ANY(bosch_bme280);
```

Zephyr already ships a BME280 driver. There is no sensor driver to write. The Kconfig entry `CONFIG_BME280=y` pulls it in.

## Kconfig

`prj.conf` selects subsystems at build time. The relevant entries for this project:

```
CONFIG_BT=y
CONFIG_BT_PERIPHERAL=y
CONFIG_BT_DEVICE_NAME="SensorNode"
CONFIG_SENSOR=y
CONFIG_BME280=y
CONFIG_PM=y
CONFIG_PM_DEVICE=y
```

Kconfig selections pull in source files and set compile-time constants. There is no manual linking of driver files. The build system handles it. This is cleaner than managing `CMakeLists.txt` file lists by hand but requires understanding the Kconfig symbol tree when something is missing.

## Custom GATT Service

The service uses a 128-bit UUID with three characteristics: temperature, humidity, and pressure. On each new reading the peripheral notifies any connected central.

Connection and disconnection callbacks restart advertising automatically. A connected central stops receiving notifications if it does not subscribe via the CCCD. The application checks the subscription state before calling `bt_gatt_notify`.

```c
static void on_connected(struct bt_conn *conn, uint8_t err)
{
    if (err) return;
    current_conn = bt_conn_ref(conn);
}

static void on_disconnected(struct bt_conn *conn, uint8_t reason)
{
    bt_conn_unref(current_conn);
    current_conn = NULL;
    bt_le_adv_start(&adv_param, ad, ARRAY_SIZE(ad), NULL, 0);
}
```

## Power Management

The goal is sub-10µA average current at one reading per minute. Between readings the application calls `k_sleep()` and Zephyr's PM subsystem negotiates the deepest idle state the hardware supports given active devices and pending wakeups.

The Nordic PPK2 (Power Profiler Kit 2) measures current with nA resolution and timestamps. It makes the sleep behavior visible: you can see the transmission spike, the active processing period, and the flat sleep floor. Without a measurement tool you are guessing.

The BLE connection interval and advertising interval both affect average current. Faster advertising shortens the time a central takes to connect but increases the duty cycle in non-connected mode. For a sensor node that rarely needs connection, slow advertising intervals and long connection intervals are the right tradeoff.

## Build

```bash
west build -b nrf52840dk_nrf52840 app
west flash
```

Zephyr's west tool handles CMake configuration, build, and flashing. The nRF Connect SDK wraps Zephyr and adds Nordic-specific drivers and libraries. Using the DK with its onboard J-Link means no external programmer needed.

---

[View project](/hardware/zephyr-ble-sensor/)
