---
layout: post
title: "FreeRTOS on STM32F411RE: Queues, Watchdogs, and Flash Config"
---

Multi-task firmware on the STM32F411RE Nucleo using FreeRTOS. Five tasks, a hardware watchdog, queue-based inter-task communication, and config stored in flash with CRC validation.

![FreeRTOS task architecture](/assets/images/projects/freertos-stm32-tasks.svg)

## Task Architecture

Five tasks run concurrently. The sensor task reads a BME280 over I2C on a 1000ms period using `vTaskDelayUntil` for accurate cadence. It pushes readings into a queue consumed by the processing task, which applies a calibration offset from flash config and formats the data as a string. That string goes into a second queue consumed by the UART print task.

Separating print into its own task matters. UART at 115200 baud takes ~87µs per byte. Blocking a sensor or processing task on serial output breaks timing. The dedicated UART task absorbs the latency without stalling producers.

```c
typedef struct {
    uint32_t timestamp_ms;
    int32_t  temperature_cdeg;
    uint32_t humidity_cpct;
    uint32_t pressure_pa;
} sensor_reading_t;
```

A CLI task provides a UART shell. Commands: `status`, `config get`, `config set`, `reset`. Input arrives via a character queue fed by the UART RX ISR using `xQueueSendFromISR`, the standard pattern for safe ISR-to-task data passing in FreeRTOS.

## Watchdog Supervision

The watchdog supervisor runs at priority 4, the highest in the system. Every other task runs at priority 1 or 2. The supervisor wakes every 500ms and checks a `volatile uint32_t` bitmask. Each task has one bit. When a task completes its work cycle it calls a checkin function that sets its bit atomically.

If all bits are set at the 500ms deadline, the supervisor clears the bitmask and feeds the IWDG. If any bit is missing, it logs which task failed and deliberately does not feed the watchdog. The system resets within 2 seconds.

```c
void watchdog_checkin(uint32_t task_bit)
{
    checkin_flags |= task_bit;  // atomic on Cortex-M4 for aligned 32-bit writes
}
```

A watchdog fed unconditionally from one place only catches total system hangs. This pattern catches hangs in any individual task. The supervisor itself can starve if a lower-priority task holds a mutex it needs, but the supervisor holds no mutexes, which makes it safe.

## Flash Config

Config lives in the last flash page. At boot the driver validates the magic number and CRC32. On mismatch it falls back to compiled-in defaults.

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

Writing requires erasing the page first. The STM32F4 flash controller needs the page unlocked via the key sequence, then erased, then written in 4-byte aligned chunks. Getting this wrong produces hard faults or silently corrupted config. The CRC check on boot catches corruption and handles it cleanly rather than running with garbage values.

## Build System

CMake with `arm-none-eabi-gcc`. FreeRTOS is a git submodule. HAL and CMSIS sources come from STM32CubeMX output but the generated Makefile is discarded. CMake makes it straightforward to add FreeRTOS as a subdirectory and keep the build reproducible without re-running CubeMX on every dependency change.

The allocator is `heap_4`. Total heap is 24KB, all allocated at task creation. Nothing is dynamically allocated at runtime.

---

[View project](/hardware/freertos-stm32/)
