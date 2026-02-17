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

<!-- Compact Header -->
<header class="page-header">
  <div class="header-content">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh" class="avatar">
    <div class="header-text">
      <h1>Hugh</h1>
      <p class="subtitle">Building cloud infrastructure and distributed systems</p>
    </div>
  </div>
  <div class="header-links">
    <a href="https://github.com/HueCodes">github</a>
    <a href="mailto:huecodes@proton.me">email</a>
    <a href="/blog">writing</a>
  </div>
</header>

<!-- Bio -->
<section class="bio">
  <p>Go developer focused on cloud infrastructure, networking, and distributed systems. Contributing to production OSS like Cilium eBPF. Based in Seattle. When not coding: mountains, grappling, ultra marathons.</p>
</section>

<!-- Project Cards with Visual Elements -->
<section class="projects-showcase">
  <h2>projects</h2>

  <div class="project-grid">
    <!-- Project Card 1 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">🔒</span>
        <div>
          <h3><a href="https://github.com/HueCodes/Go-Hunter">Go-Hunter</a></h3>
          <span class="tech-badge go">Go</span>
        </div>
      </div>
      <p class="card-description">Multi-cloud attack surface management platform</p>
      <ul class="card-features">
        <li>1K assets/min across AWS/GCP/Azure/Cloudflare/DO</li>
        <li>SHA256 drift detection</li>
        <li>Multi-tenant SaaS with encrypted credentials</li>
      </ul>
      <div class="card-footer">
        <span class="metric">🚀 1K assets/min</span>
        <a href="/2026/01/31/go-hunter.html">read more →</a>
      </div>
    </div>

    <!-- Project Card 2 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">⚡</span>
        <div>
          <h3><a href="https://github.com/HueCodes/forge-db">Forge-DB</a></h3>
          <span class="tech-badge rust">Rust</span>
        </div>
      </div>
      <p class="card-description">SIMD-accelerated vector database</p>
      <ul class="card-features">
        <li>IVF-PQ + HNSW indexing with AVX2/AVX-512</li>
        <li>13K QPS at 75µs latency</li>
        <li>32x memory compression, zero deps</li>
      </ul>
      <div class="card-footer">
        <span class="metric">📊 13K QPS</span>
        <a href="/2026/01/31/forge-db.html">read more →</a>
      </div>
    </div>

    <!-- Project Card 3 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">🤖</span>
        <div>
          <h3><a href="https://github.com/HueCodes/nodix">Nodix</a></h3>
          <span class="tech-badge rust">Rust</span>
        </div>
      </div>
      <p class="card-description">Real-time compute graph for robotics</p>
      <ul class="card-features">
        <li>DAG execution with EDF/RM scheduling</li>
        <li>5K+ iter/sec, <1ms p99 latency</li>
        <li>Zero-copy data flow</li>
      </ul>
      <div class="card-footer">
        <span class="metric">⚡ <1ms p99</span>
        <a href="/2026/01/31/nodix.html">read more →</a>
      </div>
    </div>

    <!-- Project Card 4 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">🌐</span>
        <div>
          <h3><a href="https://github.com/HueCodes/Rust-ServiceMesh">ServiceMesh</a></h3>
          <span class="tech-badge rust">Rust</span>
        </div>
      </div>
      <p class="card-description">L7 proxy with circuit breakers</p>
      <ul class="card-features">
        <li>HTTP/2, gRPC support</li>
        <li>Lock-free atomics + DashMap</li>
        <li>Prometheus metrics, graceful shutdown</li>
      </ul>
      <div class="card-footer">
        <span class="metric">💨 52M ops/sec</span>
        <a href="#">github →</a>
      </div>
    </div>

    <!-- Project Card 5 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">🔧</span>
        <div>
          <h3><a href="https://github.com/HueCodes/gretun">Gretun</a></h3>
          <span class="tech-badge go">Go</span>
        </div>
      </div>
      <p class="card-description">GRE tunnel management CLI</p>
      <ul class="card-features">
        <li>Create and manage site-to-site tunnels</li>
        <li>Cloud VPC interconnection via netlink</li>
        <li>Tunnel health probing</li>
      </ul>
      <div class="card-footer">
        <span class="metric">🔗 Networking</span>
        <a href="/2026/01/31/gretun.html">read more →</a>
      </div>
    </div>

    <!-- Project Card 6 -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">📡</span>
        <div>
          <h3><a href="https://github.com/HueCodes/Sensor-Bridge">Sensor-Bridge</a></h3>
          <span class="tech-badge rust">Rust</span>
        </div>
      </div>
      <p class="card-description">Lock-free sensor fusion pipeline</p>
      <ul class="card-features">
        <li>4-stage IMU/LIDAR fusion</li>
        <li>2.2B items/sec throughput</li>
        <li>no_std compatible</li>
      </ul>
      <div class="card-footer">
        <span class="metric">🚄 2.2B/sec</span>
        <a href="#">github →</a>
      </div>
    </div>

    <!-- Project Card 7 - New addition -->
    <div class="card">
      <div class="card-header">
        <span class="card-icon">📦</span>
        <div>
          <h3><a href="https://github.com/HueCodes/Compression-Algo-Huffman">Huffman-Cpp</a></h3>
          <span class="tech-badge cpp">C++17</span>
        </div>
      </div>
      <p class="card-description">Modern Huffman coding implementation</p>
      <ul class="card-features">
        <li>Smart pointers, priority queue</li>
        <li>17 comprehensive unit tests</li>
        <li>Educational compression algorithm</li>
      </ul>
      <div class="card-footer">
        <span class="metric">✅ 100% tests</span>
        <a href="/2026/02/16/huffman-cpp.html">read more →</a>
      </div>
    </div>
  </div>
</section>

<!-- Compact OSS Section -->
<section class="oss-compact">
  <h2>open source contributions</h2>
  <div class="oss-grid">
    <a href="https://github.com/cilium/ebpf/pull/1945" class="oss-badge">
      <strong>cilium/ebpf</strong>
      <span>poller interfaces</span>
    </a>
    <a href="https://github.com/tokio-rs/tokio/pull/7874" class="oss-badge">
      <strong>tokio</strong>
      <span>lock-free cancellation</span>
    </a>
    <a href="https://github.com/hyperium/hyper/pull/4011" class="oss-badge">
      <strong>hyper</strong>
      <span>RFC 9110 compliance</span>
    </a>
    <a href="https://github.com/rust-lang/rust-clippy/pull/16402" class="oss-badge">
      <strong>clippy</strong>
      <span>proc-macro lint fix</span>
    </a>
    <a href="https://github.com/dimforge/rapier/pull/806" class="oss-badge">
      <strong>rapier</strong>
      <span>friction simulation</span>
    </a>
    <a href="https://github.com/RustPython/RustPython/pull/6661" class="oss-badge">
      <strong>rustpython</strong>
      <span>set operators fix</span>
    </a>
  </div>
  <p class="oss-footer"><a href="#all-contributions">+ 3 more contributions</a></p>
</section>

<!--
OPTION 3: "Visual Card Grid"
- Modern card-based layout with icons
- Each project gets a card with metrics/features
- Tech badges for quick scanning
- Compact OSS contribution badges
- Links to blog posts for detailed writeups
- Includes new Huffman-Cpp project
-->
