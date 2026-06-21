---
layout: default
title: builds
---

<nav>
  <a href="/">home</a>
  <a href="/projects">projects</a>
  <a href="/builds">builds</a>
  <a href="/blog">blog</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>builds</h1>
  <p>workbench notes, hardware bits, software experiments, and things in motion.</p>
</div>

<div class="build-stream">
  <article class="build-entry">
    <div class="build-meta">jun 2026</div>
    <div class="build-body">
      <h2>ringtuner</h2>
      <p>working on a small c simulator for nic rx interrupt coalescing. the fun part is the tradeoff: low interrupt delay helps latency until cpu overhead and ring pressure start hurting everything else.</p>
      <p>current pass is docs and comparison work: fixed baselines, tuned thresholds, trace replay notes, and a napi polling model.</p>
    </div>
  </article>

  <article class="build-entry">
    <div class="build-meta">may 2026</div>
    <div class="build-body">
      <h2>slam-engine</h2>
      <p>reframing this as embedded occupancy mapping and pose estimation instead of pretending cheap ultrasonic sensors make a clean full slam story.</p>
      <p>the host c core already has an ekf, log-odds grid, simulator frames, and tests. next useful step is feeding it real scans and encoder-ish odometry.</p>
      <img src="/assets/images/projects/slam-engine.svg" alt="slam-engine architecture sketch" class="build-media">
    </div>
  </article>

  <article class="build-entry">
    <div class="build-meta">apr 2026</div>
    <div class="build-body">
      <h2>esp32-s3 rf board</h2>
      <p>cleaning up the bare-chip esp32-s3 board before treating it as a real hardware artifact. mostly drc triage, manufacturing handoff notes, and making the rf story easy to inspect.</p>
      <img src="/assets/images/projects/esp32-s3-rf-board.jpg" alt="esp32-s3 rf development board" class="build-media">
    </div>
  </article>

  <article class="build-entry">
    <div class="build-meta">mar 2026</div>
    <div class="build-body">
      <h2>ping-rs</h2>
      <p>servo-swept ultrasonic radar on an stm32f411re in async rust. hc-sr04 readings go to an oled polar plot and usb serial telemetry.</p>
      <p>i want one clean demo artifact from this: the physical sweep, the oled output, and a host-side replay of the json stream.</p>
    </div>
  </article>

  <article class="build-entry">
    <div class="build-meta">feb 2026</div>
    <div class="build-body">
      <h2>gatewind</h2>
      <p>drone rl environment work: procedural gates, wind disturbance, baseline controllers, ppo training, and portable trajectory artifacts.</p>
      <p>next cleanup is picking the best replay and writing the short version of what the environment is actually testing.</p>
    </div>
  </article>

  <article class="build-entry">
    <div class="build-meta">jan 2026</div>
    <div class="build-body">
      <h2>archimedes</h2>
      <p>browser computational geometry in rust, wasm, and webgpu. convex hulls, delaunay, polygon ops, robust predicates, and crdt-backed collaboration.</p>
      <img src="/assets/images/projects/archimedes-critical-area.png" alt="archimedes critical area view" class="build-media">
    </div>
  </article>
</div>
