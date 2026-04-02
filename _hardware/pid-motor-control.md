---
layout: hardware
title: PID Motor Control
status: complete
github:
---

Closed-loop PID motor controller on **STM32 Nucleo-F411RE** with real-time IMU feedback. Live tuning via UART CLI, step response capture to CSV for plotting.

## Hardware

| Component | Part | Interface |
|-----------|------|-----------|
| MCU | STM32 Nucleo-F411RE | - |
| Motor Driver | TB6612FNG | GPIO + PWM |
| IMU | MPU-6050 (6-axis) | I2C1 @ 400 kHz |
| Display | SSD1306 0.96" OLED | I2C1 @ 0x3C |
| Motor | 130-type DC | via TB6612FNG |
| Debug | ST-Link VCP (USART2) | 115200 baud |

## Architecture

```
TIM3_CH1 (20 kHz PWM) --> TB6612FNG --> DC Motor
                                          |
MPU-6050 (I2C, 14-byte burst read) <-----+
    |                                  (physical coupling)
    v
Complementary Filter (alpha=0.98)
    |
    v
PID Controller (1 kHz, TIM2 ISR)
    |  - Derivative on measurement
    |  - Low-pass filter on D term (N=10)
    |  - Integral clamping anti-windup
    v
Motor effort (-1.0 to +1.0) --> sign-magnitude drive
```

## Control Design

The PID loop runs at 1 kHz in TIM2's update ISR. Three techniques keep the output clean:

**Derivative on measurement** -- the D term differentiates the process variable, not the error signal. This eliminates the derivative kick that occurs when the setpoint changes abruptly.

**Low-pass filter on D term** -- a first-order filter with N=10 smooths high-frequency noise from the IMU without adding significant phase lag at the control bandwidth.

**Integral anti-windup** -- the integrator is clamped to the output range. When the motor saturates, the integrator stops accumulating, preventing overshoot on recovery.

## UART CLI

The main loop runs a serial command interface for live tuning:

| Command | Description |
|---------|-------------|
| `kp <value>` | Set proportional gain |
| `ki <value>` | Set integral gain |
| `kd <value>` | Set derivative gain |
| `sp <angle>` | Set target angle |
| `step` | Trigger step response, stream CSV |
| `status` | Print current gains, setpoint, error |

Step response data streams as CSV over UART for offline plotting with `scripts/plot_step_response.py`.

## Build

```bash
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Debug ..
make
```

Flash via ST-Link SWD.
