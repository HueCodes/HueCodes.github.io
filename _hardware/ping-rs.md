---
layout: hardware
title: ping-rs — Servo-Swept Ultrasonic Radar
status: v0 — sweeping, rendering, streaming
github: https://github.com/HueCodes/ping-rs
---

A servo-swept ultrasonic radar built on **STM32F411RE** in async Rust (Embassy), with a live polar-plot display on a 0.96" OLED and streaming JSON telemetry over USB.

An HC-SR04 ultrasonic rangefinder rides on top of an SG90 servo. The servo sweeps 0 → 180 → 0 degrees (~6 s per full scan) while the sensor fires one ping every 50 ms. Each (angle, distance) sample is rasterized to a polar plot on the SSD1306 OLED and simultaneously streamed out the USB CDC ACM port as a JSON line:

```
{"t_us":1234567,"angle_deg":45,"dist_mm":823}
```

## Hardware

| Part | Purpose |
|---|---|
| STM32 Nucleo-F411RE | MCU + onboard ST-Link for flashing and RTT log |
| HC-SR04 | Ultrasonic time-of-flight rangefinder |
| SG90 | Micro servo, 180 deg sweep |
| SSD1306 0.96" OLED 128×64 | Polar radar display, I2C |
| USB-A to Micro-USB | Power + CDC telemetry to host |
| Logic analyzer (8CH) | Timing capture for the proof-of-correctness screenshot |

## Wiring (Nucleo-F411RE)

| Signal | Nucleo pin | MCU pin |
|---|---|---|
| Servo PWM | CN5 D1 | PA0 (TIM2_CH1) |
| HC-SR04 Trig | CN8 A1 | PA1 |
| HC-SR04 Echo | CN9 A3 | PB0 |
| OLED SCL | CN10 D15 | PB8 (I2C1_SCL) |
| OLED SDA | CN10 D14 | PB9 (I2C1_SDA) |
| USB D+/D- | USB | PA11 / PA12 |
| Status LED | onboard | PA5 (LD2) |

HC-SR04 needs 5V; its echo line also outputs 5V and is level-shifted to 3.3V with a 1k/2k resistor divider into PB0.

## Software architecture

Three Embassy tasks over a shared SPSC channel:

```
    servo::sweep_task            -----> CURRENT_ANGLE (AtomicU8)
                                            |
                                            v
    ultrasonic::ping_task  -- Sample --> CHANNEL (64 deep)
                                            |
                           +----------------+---------------+
                           v                                v
              display::render_task             telemetry::usb_task
              (SSD1306, 30 fps)                (USB CDC, 1:1 sample rate)
```

Dropping samples is intentional: the ping task uses `try_send`, so if display or USB stalls, the radar keeps scanning and old readings are discarded rather than backing up the pipeline.

## Build and flash

```sh
rustup target add thumbv7em-none-eabihf
cargo install probe-rs --features cli
cargo run --release         # flashes via onboard ST-Link, attaches RTT log
```

RTT log prints `defmt` messages over the ST-Link probe; the USB CDC port is a separate interface used only for telemetry.

## Reading telemetry

On macOS the USB CDC shows up as `/dev/tty.usbmodem*`:

```sh
cat /dev/tty.usbmodem*   # raw JSON lines
```
