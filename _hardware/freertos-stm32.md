---
layout: hardware
title: FreeRTOS STM32
status: in progress
github:
---

## Overview

Multi-task FreeRTOS firmware on STM32F411RE demonstrating production-relevant patterns: task communication via queues, ISR-safe data passing, watchdog supervision, and persistent configuration in flash.

## Hardware

| Component | Description |
|-----------|-------------|
| Board | STM32 Nucleo-64 (STM32F411RE) |
| Core | ARM Cortex-M4 @ 100MHz |
| Sensor | BME280 over I2C (temp/humidity/pressure) |
| Debug | ST-Link onboard, UART2 virtual COM |

## Task Architecture

```
[Sensor P=2] --sensor_queue(4)--> [Processing P=2] --print_queue(8)--> [UART TX P=1]
                                                                              ^
[CLI P=2]    --------------------------------print_queue(8)------------------>|
[UART RX ISR] --rx_char_queue(32)--> [CLI P=2]

[Watchdog P=4] -- supervises all tasks -- feeds IWDG on full checkin
```

## Tasks

| Task | Priority | Stack | Description |
|------|----------|-------|-------------|
| Sensor | 2 | 512w | BME280 I2C read, 1000ms period via vTaskDelayUntil |
| Processing | 2 | 512w | Applies flash config offsets, formats output |
| UART TX | 1 | 256w | Dedicated print task — UART is slow, never block producers |
| Watchdog | 4 | 256w | Feeds IWDG only when all tasks check in via bitmask |
| CLI | 2 | 512w | UART shell: status, config get/set, task suspend |

## Watchdog Pattern

The watchdog supervisor (highest priority) checks a `volatile uint32_t` bitmask every 500ms. Each task sets its bit atomically. If all bits are set, the supervisor clears them and feeds the IWDG. If any bit is missing at the deadline, the IWDG is not fed and the system resets in ~2 seconds. The UART logs which task failed to check in before reset.

A watchdog fed unconditionally from one place is nearly useless. This pattern catches hangs in any individual task.

## Persistent Config

Config struct stored in the last flash page with CRC32 validation:

```c
typedef struct {
    uint32_t magic;           // 0xDEADBEEF
    uint32_t version;
    int32_t  temp_offset_cdeg;
    uint32_t sample_period_ms;
    uint32_t uart_baud;
    uint32_t crc32;
} device_config_t;
```

On boot: validate magic + CRC, fall back to defaults on corruption.

## Build System

CMake + arm-none-eabi-gcc. FreeRTOS as a git submodule. HAL/CMSIS sources from STM32CubeMX (generation only — Makefile discarded). `heap_4` allocator: 24KB heap, all allocations at task creation, nothing dynamic at runtime.
