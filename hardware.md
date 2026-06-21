---
layout: default
title: hardware
---

<nav>
  <a href="/">home</a>
  <a href="/projects">projects</a>
  <a href="/builds">builds</a>
  <a href="/blog">blog</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>hardware</h1>
  <p>hardware and embedded projects.</p>
</div>

<div class="section">
  <div class="project">
    <a href="/hardware/esp32-s3-rf-board/">ESP32-S3 RF Development Board</a>
    <span>4-layer rf board with bare ESP32-S3 die, 50-ohm feedline, and chip antenna, designed in KiCAD</span>
  </div>
  <div class="project">
    <a href="/hardware/ping-rs/">ping-rs</a>
    <span>hc-sr04, sg90, and ssd1306 on stm32f411re in async rust, with polar display and json-over-usb telemetry</span>
  </div>
  <div class="project">
    <a href="/hardware/imu-deadreckoning/">IMU Dead Reckoning</a>
    <span>strap-down inertial nav on stm32f411re in async rust, using mpu-6050, madgwick ahrs, and world-frame integration</span>
  </div>
  <div class="project">
    <a href="/hardware/slam-engine/">SLAM Engine</a>
    <span>stm32-scale occupancy mapping and pose estimation with an ekf, log-odds grid, and host simulator</span>
  </div>
  <div class="project">
    <a href="/hardware/pid-motor-control/">PID Motor Control</a>
    <span>closed-loop pid controller on stm32f411re with imu feedback, uart tuning, and step response capture</span>
  </div>
  <div class="project">
    <a href="/hardware/uart-stm32-driver/">STM32 Bare-Metal UART Driver</a>
    <span>interrupt-driven uart with lock-free spsc ring buffers on stm32f401, no hal, no rtos, 47 host tests</span>
  </div>
</div>
