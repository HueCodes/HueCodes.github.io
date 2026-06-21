---
layout: default
title: projects
---

<nav>
  <a href="/">home</a>
  <a href="/projects">projects</a>
  <a href="/builds">builds</a>
  <a href="/blog">blog</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>projects</h1>
  <p>selected systems, robotics, ml, and security work.</p>
</div>

<div class="section">
  <h2>systems</h2>

  <div class="project">
    <a href="https://github.com/HueCodes/Aulon">aulon</a>
    <span>nats-compatible message broker built on tokio-uring, fixed buffers, sharded routing, fuzzed wire parsing, operator docs, and benchmark harnesses</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/ringtuner">ringtuner</a>
    <span>c simulator for nic rx interrupt coalescing with fixed baselines, adaptive tuning, trace replay notes, and latency/drop/cpu tradeoff analysis</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/gretun">gretun</a>
    <span>go cli for nat-traversing gre-over-fou tunnels with a small coordinator, sealed discovery messages, metrics, and linux netlink integration</span>
  </div>
  <div class="project">
    <a href="https://huecodes.github.io/Archimedes/">archimedes</a>
    <span>rust, wasm, and webgpu computational-geometry playground with hulls, delaunay, voronoi, polygon ops, robust predicates, and crdt collaboration</span>
  </div>
</div>

<div class="section">
  <h2>robotics and embedded</h2>

  <div class="project">
    <a href="/hardware/ping-rs/">ping-rs</a>
    <span>stm32f411re ultrasonic radar in async rust: servo sweep, hc-sr04 ranging, ssd1306 polar display, and json-over-usb telemetry</span>
  </div>
  <div class="project">
    <a href="/hardware/imu-deadreckoning/">imu-deadreckoning</a>
    <span>strap-down inertial navigation on stm32f411re with mpu-6050 sampling, madgwick ahrs, world-frame integration, and drift accounting</span>
  </div>
  <div class="project">
    <a href="/hardware/slam-engine/">slam-engine</a>
    <span>stm32-scale occupancy mapping and pose estimation with an ekf, log-odds grid, host simulator, replayable frames, and ci-covered c core</span>
  </div>
  <div class="project">
    <a href="/hardware/esp32-s3-rf-board/">esp32-s3 rf board</a>
    <span>4-layer bare-chip esp32-s3 board with usb-uart, power, antenna keepout, and a controlled-impedance 2.4 ghz feedline</span>
  </div>
  <div class="project">
    <a href="/hardware/pid-motor-control/">pid motor control</a>
    <span>stm32f411re closed-loop motor controller with 1 khz pid, imu feedback, uart tuning, and step-response capture</span>
  </div>
</div>

<div class="section">
  <h2>ml</h2>

  <div class="project">
    <a href="https://github.com/HueCodes/Gatewind">gatewind</a>
    <span>fast 3d drone rl environments with vectorized dynamics, procedural gates, wind disturbances, baseline controllers, ppo training, and portable trajectory artifacts</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Crucible">crucible</a>
    <span>grpo reasoning trainer for small instruct models on local hardware, with verifier rewards, mps supervision, tests, and phase-one training curves</span>
  </div>
</div>

<div class="section">
  <h2>security and tools</h2>

  <div class="project">
    <a href="https://github.com/HueCodes/Dredge">dredge</a>
    <span>binary and firmware vulnerability triage pipeline with ghidra decompilation, profile-specific heuristics, llm review, resumable scans, reports, and tests</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Sensor-Bridge">sensor-bridge</a>
    <span>lock-free sensor processing pipeline with spsc ring buffers, no_std-compatible core pieces, benchmarks, examples, and robotics-oriented backpressure paths</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Container-runtime-rs">container-runtime-rs</a>
    <span>minimal rust container runtime exploring namespaces, cgroups v2, overlayfs, oci image pulling, lifecycle state, and linux hardening primitives</span>
  </div>
</div>

<div class="section">
  <h2>archive</h2>

  <div class="project">
    <a href="/opensource/">open source contributions</a>
    <span>recent upstream work and older contribution notes</span>
  </div>
  <div class="project">
    <a href="/hardware/">hardware index</a>
    <span>older embedded notes, board ideas, and hardware writeups</span>
  </div>
</div>
