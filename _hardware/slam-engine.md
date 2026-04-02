---
layout: hardware
title: SLAM Engine
status: updates soon
github:
---

Indoor simultaneous localization and mapping on **STM32F411RE** using ultrasonic range scanning and IMU odometry. EKF state estimation with occupancy grid mapping rendered on an OLED.

## Hardware

| Component | Part | Interface |
|-----------|------|-----------|
| MCU | STM32 Nucleo-F411RE | Cortex-M4F, 100 MHz |
| Range sensor | HC-SR04 ultrasonic | GPIO trigger/echo |
| Servo | SG90 micro (180 deg sweep) | PWM |
| IMU | MPU-6050 | I2C |
| Display | SSD1306 0.96" OLED | I2C |
| Storage | Micro SD card module | SPI |
| Motor | DC motor + L298N driver | PWM + GPIO |

## Architecture

```
 IMU (100Hz)         Scanner (~2Hz)
   |                     |
   v                     v
 Complementary       Servo sweep
 Filter              HC-SR04 readings
   |                     |
   v                     v
 +---------------------------+
 |        EKF SLAM           |
 |  State: [x, y, theta, v]  |
 |  Predict: IMU motion model |
 |  Update: scan matching     |
 +---------------------------+
        |            |
        v            v
  Occupancy Grid   SD Card
  (log-odds, SRAM)  (periodic flush)
        |
        v
    SSD1306 OLED
    (top-down map)
```

## Occupancy Grid

4m x 4m area, 5cm resolution: 80x80 cells = 6400 bytes in SRAM. Log-odds representation (int8_t, clamped -127 to +127) with Bresenham ray casting for updates.

## FreeRTOS Tasks

| Task | Priority | Rate | Description |
|------|----------|------|-------------|
| imu_task | High | 100 Hz | MPU-6050 read + complementary filter |
| scan_task | Medium | ~2 Hz | Servo sweep + HC-SR04 range readings |
| slam_task | Medium | On new scan | EKF predict/update + grid update |
| display_task | Low | 5 Hz | Render occupancy grid to OLED |

## Milestones

1. Bringup -- IMU + complementary filter, servo sweep, OLED static display
2. Scan + grid -- build occupancy grid from known position
3. EKF integration -- state estimation with IMU predict + scan update
4. Map persistence -- SD card save/load of occupancy grid
5. Mobile platform -- integrate with PID motor control, add wheel encoder
6. Loop closure -- detect revisited areas, correct accumulated drift

## Known Limitations

- No wheel encoder yet (IMU-only odometry drifts). Highest-priority future addition.
- HC-SR04 15-degree beam width limits angular resolution vs LIDAR.
- ~2Hz scan rate limits movement speed before map quality degrades.
