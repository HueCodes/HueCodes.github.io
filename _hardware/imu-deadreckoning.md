---
layout: hardware
title: IMU Dead Reckoning
status: v0.1 — host harness + measured drift baseline
github: https://github.com/HueCodes/imu-deadreckoning
---

Strap-down inertial dead-reckoning on **STM32F411RE** in async Rust (Embassy). MPU-6050 over I2C at 100 Hz, Madgwick AHRS, double-integration of world-frame acceleration to a position estimate. No allocator, no RTOS.

The point of the demo is the **drift**. A consumer-grade MEMS IMU integrated naively accumulates metres of position error per minute. That is exactly why production AUVs spend money on Doppler velocity logs, ring-laser gyros, and acoustic positioning (USBL/LBL): an inertial-only solution diverges fast, and the whole nav stack is built around bounding that divergence with periodic aiding fixes.

## Why this exists

Inertial nav is the core fallback for any vehicle that loses absolute position reference: AUVs underwater (no GPS), indoor robots, GPS-denied flight. The same code path — attitude from gyro+accel, world-frame integration, drift accounting — is what runs on a Mako-class AUV between DVL pings. This project shows the embedded-Rust shape of that loop in a form small enough to read in one sitting.

## Pipeline

```
MPU-6050 ── I2C @ 100 Hz ──> imu_task ──[ImuSample]──> ahrs_task ──[Attitude]──> nav_task ──> defmt RTT @ 1 Hz
```

Three Embassy tasks, two `embassy-sync` channels.

- `mpu6050::imu_task` configures the part, runs a 2 s gyro-bias calibration while the board is held still, then samples accel + gyro at 100 Hz.
- `ahrs::ahrs_task` runs the IMU-only Madgwick filter (gyro + accel, no magnetometer) to produce a unit quaternion.
- `nav::nav_task` rotates body-frame accel into the world frame using the current quaternion, subtracts gravity, integrates to velocity and position, and logs roll/pitch/yaw + v + p once per second.

## Hardware

| Component | Part | Interface |
|-----------|------|-----------|
| MCU | ST Nucleo-F411RE | Cortex-M4F, 100 MHz |
| IMU | MPU-6050 breakout (GY-521) | I2C1 @ 400 kHz |
| Debug | onboard ST-Link, RTT | `defmt` over SWO |

Wiring (Nucleo Arduino headers):

| Nucleo | MPU-6050 |
| ------ | -------- |
| 3.3V | VCC |
| GND | GND |
| PB8 (D15) | SCL |
| PB9 (D14) | SDA |
| GND | AD0 (selects address 0x68) |

## Honest characterisation

What this implementation will **not** do:

- Bound position drift. Without aiding (DVL, USBL, GPS, ZUPT), the position estimate is an open-loop integration of accelerometer bias and noise. Expect metres-per-minute drift on this part.
- Estimate yaw absolutely. Madgwick-IMU (no magnetometer) corrects only roll and pitch; yaw drifts with the gyro.
- Handle vibration well. The default DLPF is 44 Hz; a vehicle with motor noise above that band needs additional filtering.

What it does do:

- Produce a stable, gravity-aligned attitude estimate while stationary or moving smoothly.
- Demonstrate the strap-down integration loop in ~150 lines of `no_std` Rust.
- Make the drift visible — the 1 Hz log is the artefact.

## Host validation harness

The pure algorithm (Madgwick + strap-down integrator) lives in `src/algo.rs`, shared between the firmware and a host-side simulator at `host/`. The sim drives the same code with synthetic 100 Hz IMU samples for a stationary vehicle, corrupted with noise + bias representative of an MPU-6050 after a 2 s calibration: gyro residual bias 0.1 deg/s, gyro noise 0.05 deg/s rms, accel bias 50 mg, accel noise 0.04 m/s² rms.

```sh
cd host
cargo run --release --bin drift_sim > drift.csv
```

Default 60 s run, seed=1, one-line summary to stderr:

```
duration=60s seed=1 | final |p|=203.03 m | horizontal drift=3.59 m | vz=6.79 m/s
```

The horizontal drift (~3.6 m / 60 s) is the headline number: open-loop, no aiding, the position estimate diverges at metres-per-minute, exactly as an inertial-only solution should. The vertical channel diverges far faster because a gravity-aligned IMU-only AHRS cannot observe accel-z bias; production stacks fix this with a barometer or depth sensor. Both failure modes are why a fielded UUV or UAV pairs strap-down INS with DVL, GPS, USBL, or air-data.

## Roadmap

- **v0** — MPU-6050 + Madgwick + naive integration, `defmt` logs.
- **v0.1** — host harness, measured stationary drift baseline.
- **v1** — zero-velocity update (ZUPT) detection: when `|a − g|` and `|w|` are both small for N samples, snap velocity to zero. Cheapest drift-bounding aid; used in real pedestrian INS. The harness becomes the regression test (drift should drop ~10× under stationary).
- **v2** — repackage as an example in [Sensor-Bridge](https://github.com/HueCodes/Sensor-Bridge), using its lock-free pipeline for the IMU → AHRS → Nav stages.
- **v3** — stream samples + state over USB CDC for offline plotting; produce a drift curve from real hardware to compare against the synthetic baseline.
