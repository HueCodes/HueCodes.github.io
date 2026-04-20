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
      <p>building systems software, networking, and embedded firmware. designing and building hardware.</p>
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
    <summary><strong>archimedes</strong> — interactive computational geometry in the browser</summary>
    <p>rust → wasm32 via trunk, egui on wgpu. five tabs: andrew's monotone chain convex hull with step-through animation and point-line duality view; incremental bowyer-watson delaunay / voronoi (via <code>spade</code>) with power-diagram (weighted voronoi) mode and replay controls; polygon booleans via <code>i_overlay</code>; semiconductor critical-area dilation (minkowski offset + intersection); naive f32 vs. shewchuk adaptive <code>orient2d</code> predicate showdown rendering the untrustworthy band where the static error bound straddles zero. same source builds native desktop. <a href="https://huecodes.github.io/Archimedes/">live demo →</a> · <a href="https://github.com/HueCodes/Archimedes">github →</a></p>
  </details>

  <!-- ping-rs — uncomment when repo is uploaded
  <p>
    <strong>ping-rs</strong> — servo-swept ultrasonic radar<br>
    stm32 + async rust (embassy). polar oled display + json telemetry. <a href="https://github.com/HueCodes/ping-rs">github →</a>
  </p>
  -->
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.15s">
  <h2>about</h2>
  <p>i got into engineering through curiosity about how things work. started with chemistry and biology as a kid, eventually landed on computers and robots. now i'm mostly focused on networking, distributed systems, and embedded hardware. lately i've been getting into rf, control theory, and building a uuv. teaching myself more math and physics along the way.</p>
  <p>currently: networking and distributed systems in rust + go. getting into hardware, rf, and robotics. contributing upstream to smoltcp, tokio, cilium/ebpf, and others.</p>
</div>

