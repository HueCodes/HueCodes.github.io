---
layout: default
title: home
---

<nav>
  <a href="/projects">projects</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro fade-in">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>networking and distributed systems, mostly rust and go. also working on ai infra and security; building hardware on the side and contributing to projects i'm interested in.</p>
      <p>outside of engineering i like being in the mountains, grappling, and running ultra marathons. based in seattle.</p>
      <p><a href="mailto:huecodes@proton.me">huecodes@proton.me</a></p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.1s">
  <h2>highlights</h2>

  <details>
    <summary><strong>moat</strong> — mtls for ai agents</summary>
    <p>ed25519 per-agent identity, capability tokens with monotonic attenuation enforced by construction, wasmtime + wasi preview1 sandbox (fuel limits, memory caps, default-deny fs/network), sha-256 hash-chain audit log, per-sender replay protection. three-stage pep: signature and replay verification, policy binding, capability evaluation. ~30k authenticated messages/sec per core. 86 tests across 6 crates. pure rust, no c bindings. <a href="https://github.com/HueCodes/Moat">github →</a></p>
  </details>

  <details>
    <summary><strong>gretun</strong> — nat-traversing peer-to-peer gre overlay for linux</summary>
    <p>two hosts behind consumer nat run <code>gretun up --coordinator &lt;url&gt;</code> and get a direct, kernel-fastpath gre tunnel between them — no port forwarding, no static ips, no manual config. fou encapsulation wraps gre (ip proto 47) in udp so consumer nats can map it and it can be hole-punched. stun over a shared userspace udp socket discovers each node's public <code>ip:port</code>. a small http coordinator registers peers and relays sealed disco envelopes — 6-byte magic + sender curve25519 pubkey + nacl-box body, tailscale-compatible format — and holds no node private keys. hole punching: each side pings published endpoints, first pong wins; symmetric-nat detection built in. control plane: ed25519 + curve25519. data plane: pure kernel gre-over-fou. prometheus metrics, keepalives. go. <a href="https://github.com/HueCodes/gretun">github →</a></p>
  </details>

  <details>
    <summary><strong>sensor-bridge</strong> — lock-free sensor processing pipeline for robotics</summary>
    <p>4-stage pipeline (ingestion → filter → aggregation → output), each stage on its own thread, connected by wait-free spsc ring buffers with cache-line padding so the hot path has zero mutexes. zero-copy data flow via object pool, buffer pool, and arc-shared payloads. adaptive backpressure controller (block, drop, or sample) with hysteresis to avoid thrash under load. hdr-histogram latency metrics with per-stage dashboards. <code>no_std</code>-compatible core (buffer, error, sensor, stage modules) builds without <code>std</code> for embedded targets; <code>std</code> build adds channels, metrics, and the udp/tcp ingest. cargo bench on apple m-series: 2.2b items/sec stage throughput, ~20ns channel latency, 0.3ns ring-buffer push, 9ns pop. 280 tests, published on crates.io + docs.rs. mit/apache-2.0. <a href="https://github.com/HueCodes/sensor-bridge">github →</a> · <a href="https://crates.io/crates/sensor-bridge">crates.io →</a></p>
  </details>

  <details>
    <summary><strong>network-beacon</strong> — c2 beacon detection from passive network traffic</summary>
    <p>rust + tokio cli that ingests pcap and classifies flows by coefficient-of-variation on inter-packet timing — cv &lt; 0.1 is a probable c2 beacon, &gt; 1.0 is organic human traffic. ja4-style fingerprints on tls client hellos to flag non-browser tooling. dns tunneling detection via subdomain shannon entropy (&gt; 3.5 bits/char), label length, and txt/null record abuse. http beacon heuristics on post repetition, suspicious user-agents, and payload-size variance. protocol-mismatch flag for tls on non-443 ports. maxmind geoip/asn enrichment, prometheus <code>/metrics</code>, ratatui live tui, throttled+deduped webhook alerts, json/jsonl output for siem ingestion, pcap replay for offline analysis. 218 tests across 10.5k loc. <a href="https://github.com/HueCodes/Network-Beacon">github →</a></p>
  </details>

  <!-- ping-rs - uncomment when bring-up is working and repo is public
  <details>
    <summary><strong>ping-rs</strong> — servo-swept ultrasonic radar</summary>
    <p>stm32f411re nucleo in async rust (embassy). hc-sr04 rangefinder on an sg90 servo sweeps 0-180-0 deg, ~6 s per scan, one ping every 50 ms. four tasks (servo, ping, display, usb) fan (angle, distance) samples through a 64-deep spsc channel to an ssd1306 128x64 polar plot over i2c and to a usb cdc acm port as json lines <code>{"t_us":..., "angle_deg":..., "dist_mm":...}</code>. drop-on-stall via <code>try_send</code>: if the display or usb lags, the radar keeps sweeping and old samples are discarded.</p>
  </details>
  -->
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.11s">
  <h2>archimedes</h2>
  <div style="display: flex; gap: 1.25rem; align-items: flex-start; flex-wrap: wrap;">
    <a href="https://huecodes.github.io/Archimedes/" style="flex-shrink: 0;">
      <img src="/assets/images/projects/archimedes-critical-area.png" alt="Archimedes — interactive computational geometry" style="width: 200px; height: auto; border-radius: 6px; border: 1px solid var(--border); display: block;">
    </a>
    <div style="flex: 1; min-width: 240px;">
      <p style="margin: 0 0 0.4rem 0;"><a href="https://huecodes.github.io/Archimedes/"><strong>interactive computational geometry in the browser →</strong></a></p>
      <p style="margin: 0 0 0.4rem 0; font-size: 14px; color: #c9d1d9;">minkowski-style polygon dilation, convex hulls, delaunay triangulation, and polygon boolean ops with shewchuk adaptive-precision predicates. runs in-browser via webassembly.</p>
      <p style="margin: 0; font-size: 13px; color: var(--text-secondary);">rust + wasm + webgpu · <a href="https://github.com/HueCodes/Archimedes">source</a></p>
    </div>
  </div>
</div>


