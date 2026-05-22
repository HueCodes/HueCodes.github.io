---
layout: default
title: hardware
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>hardware</h1>
  <p>hardware and embedded projects.</p>
</div>

<div class="section">
  <div class="project">
    <a href="/hardware/imu-deadreckoning/">IMU Dead Reckoning</a>
    <span>strap-down inertial nav on STM32F411RE in async Rust (Embassy) — MPU-6050 + Madgwick AHRS + world-frame integration, with a host harness that measures the drift</span>
  </div>
  <div class="project">
    <a href="/hardware/ping-rs/">ping-rs — Servo-Swept Ultrasonic Radar</a>
    <span>HC-SR04 + SG90 + SSD1306 on STM32F411RE in async Rust — polar-plot display and JSON-over-USB telemetry, three Embassy tasks with drop-on-overflow</span>
  </div>
  <div class="project">
    <a href="/hardware/pid-motor-control/">PID Motor Control</a>
    <span>closed-loop PID controller on STM32F411RE with MPU-6050 IMU feedback, UART CLI tuning, and step response capture</span>
  </div>
  <div class="project">
    <a href="/hardware/esp32-s3-rf-board/">ESP32-S3 RF Development Board</a>
    <span>4-layer RF board with bare ESP32-S3 die, 50-ohm feedline, and chip antenna — designed in KiCAD</span>
  </div>
  <div class="project">
    <a href="/hardware/uart-stm32-driver/">STM32 Bare-Metal UART Driver</a>
    <span>interrupt-driven UART with lock-free SPSC ring buffers on STM32F401 — no HAL, no RTOS, 47 host tests</span>
  </div>
</div>
