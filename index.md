---
layout: default
title: home
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/research">research</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>building systems in Rust.</p>
      <p class="tagline">i ship fast and want to solve hard problems with smart people.</p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section">
  <h2>about</h2>
  <p>i enjoy spending time with my family and my dog, being in the mountains, doing math and physics, ultra running, grappling, reading, and watching movies. based in seattle.</p>
</div>

<div class="section">
  <h2>currently</h2>
  <p>writing Rust and Go for distributed systems and networking. diving into hardware. contributing to Cloudflare, Cilium, Hyper, and other open source projects I like.</p>
</div>

<div class="section">
  <h2>projects</h2>
  <div class="project">
    <a href="https://github.com/HueCodes/Rust-ServiceMesh">rust service mesh</a>
    <span>service mesh data plane with HTTP/2, TLS, circuit breaker, rate limiting, L7 routing. Tokio + Hyper + Tower</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Sensor-Bridge">sensor-bridge</a>
    <span>real-time sensor pipeline with lock-free SPSC buffer, ~50ns latency, timestamp sync for multi-sensor fusion</span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/sudoku-vision">sudoku-vision</a>
    <span>camera-to-solution sudoku scanner, PyTorch CNN for digit recognition, OpenCV grid detection, C solver compiled to WebAssembly</span>
  </div>
</div>

<div class="section">
  <h2>open source</h2>
  <div class="project">
    <a href="https://github.com/rust-lang/rust-clippy/pull/16402">rust-clippy</a>
    <span>fixed false positive lint for proc-macro generated code (merged)</span>
  </div>
  <div class="project">
    <a href="https://github.com/RustPython/RustPython/pull/6661">rustpython</a>
    <span>fixed set in-place operators with self argument (merged)</span>
  </div>
  <div class="project">
    <a href="https://github.com/dimforge/rapier/pull/806">rapier</a>
    <span>GeometricMean coefficient combine rule for friction simulation (open, passes checks)</span>
  </div>
  <div class="project">
    <a href="https://github.com/hyperium/hyper/pull/4011">hyper</a>
    <span>case-insensitive trailer field matching per RFC 9110 (open, passes checks)</span>
  </div>
  <div class="project">
    <a href="https://github.com/jeremyfix/torchcvnn/pull/47">torchcvnn</a>
    <span>added input validation to normalization layers (open, passes checks)</span>
  </div>
</div>

<div class="section">
  <p class="links">
    <a href="mailto:huecodes@proton.me">email</a>
  </p>
</div>
