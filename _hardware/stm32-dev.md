---
layout: hardware
title: STM32 Bare-Metal Development
status: in progress
github:
---

## Overview

Programming STM32 microcontrollers at the bare-metal level without frameworks or operating systems. Direct hardware access, timing-critical code, and peripheral configuration.

## Why STM32?

STM32 microcontrollers are ubiquitous in robotics and embedded systems:
- **Performance:** ARM Cortex-M cores (M0+ to M7)
- **Peripherals:** Rich set of timers, ADCs, communication interfaces
- **Availability:** Wide range of MCUs for different use cases
- **Industry standard:** Used extensively in robotics control systems

## Development Board

- **Board:** STM32F4 Discovery / Nucleo
- **Core:** ARM Cortex-M4 @ 168MHz
- **Memory:** 1MB Flash, 192KB SRAM
- **Peripherals:** GPIO, timers, ADC, DAC, UART, SPI, I2C, USB

## Projects & Exercises

### GPIO Control
- LED blinking (the "hello world" of embedded)
- Button input with interrupts
- Hardware debouncing

### Timers & PWM
- Timer configuration
- PWM for motor control
- Encoder reading for position feedback

### Communication Protocols
- **UART:** Serial communication
- **SPI:** High-speed peripheral communication
- **I2C:** Sensor interfacing

### Interrupts
- External interrupt handling
- Timer interrupts for periodic tasks
- Priority and nesting

### ADC/DAC
- Analog sensor reading
- Signal generation
- DMA for continuous sampling

## Build Log

### Current Progress

*Document your learning and projects here.*

## Bare-Metal vs. Framework Development

**Bare-metal approach:**
- Direct register access
- Full control over timing
- Understanding of hardware architecture
- No abstraction overhead

**vs. HAL/Arduino:**
- Easier to start
- Less control
- Hidden complexity

For robotics, bare-metal understanding is critical for real-time performance.

## Relevance to Robotics

STM32 is commonly used for:
- **Motor control:** PWM generation, encoder feedback
- **Sensor interfaces:** I2C/SPI communication with IMUs, cameras
- **Real-time control loops:** Sub-millisecond timing requirements
- **Communication bridges:** Between high-level processors (Jetson) and low-level actuators

Direct experience with microcontroller programming is essential for robotics hardware integration.

## What I'm Learning

*RF concepts, timing, peripheral configuration, interrupt handling, and real-time constraints.*
