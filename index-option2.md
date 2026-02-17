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

<!-- Minimal Intro -->
<div class="intro-minimal">
  <div class="intro-content">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh" class="photo-small">
    <div>
      <h1>Hugh</h1>
      <p>Go developer. Cloud infrastructure and distributed systems.</p>
      <p class="location">Seattle · <a href="mailto:huecodes@proton.me">huecodes@proton.me</a></p>
    </div>
  </div>
</div>

<!-- Two Column Layout -->
<div class="two-column-layout">
  <!-- Left Column: Main Content -->
  <div class="main-content">
    <section class="work-section">
      <h2>work</h2>

      <div class="work-item">
        <h3><a href="https://github.com/HueCodes/Go-Hunter">Go-Hunter</a></h3>
        <p class="work-meta">Multi-cloud attack surface management · Go</p>
        <p>Concurrent discovery across AWS/GCP/Azure/Cloudflare/DO at 1K assets/min. SHA256-based drift detection. Multi-tenant SaaS architecture with encrypted credential storage.</p>
        <p class="work-tech">Tech: Go, PostgreSQL, Redis, AWS SDK, GCP API, Azure SDK</p>
      </div>

      <div class="work-item">
        <h3><a href="https://github.com/HueCodes/forge-db">Forge-DB</a></h3>
        <p class="work-meta">SIMD-accelerated vector database · Rust</p>
        <p>IVF-PQ + HNSW indexing with AVX2/AVX-512 runtime detection. 13K QPS at 75µs latency, 32x memory compression. Pure Rust, zero external dependencies.</p>
        <p class="work-tech">Tech: Rust, SIMD, no_std, custom allocators</p>
      </div>

      <div class="work-item">
        <h3><a href="https://github.com/HueCodes/nodix">Nodix</a></h3>
        <p class="work-meta">Real-time compute graph for robotics · Rust</p>
        <p>DAG execution engine with EDF/Rate Monotonic scheduling. 5K+ iterations/sec, <1ms p99 latency, zero-copy data flow between nodes.</p>
        <p class="work-tech">Tech: Rust, lock-free data structures, real-time scheduling</p>
      </div>

      <div class="work-item">
        <h3><a href="https://github.com/HueCodes/Rust-ServiceMesh">Rust-ServiceMesh</a></h3>
        <p class="work-meta">L7 proxy with circuit breakers · Rust</p>
        <p>HTTP/2 and gRPC support. Lock-free atomics + DashMap achieve 52M ops/sec. Prometheus metrics, graceful shutdown, connection pooling.</p>
        <p class="work-tech">Tech: Rust, Tokio, Hyper, lock-free concurrency</p>
      </div>

      <a href="#all-projects" class="expand-link">+ 3 more projects</a>
    </section>

    <section class="oss-section">
      <h2>open source</h2>

      <div class="oss-item-detailed">
        <h3><a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf</a></h3>
        <p>Add poller and eventRing interfaces for eBPF event handling</p>
      </div>

      <div class="oss-item-detailed">
        <h3><a href="https://github.com/tokio-rs/tokio/pull/7874">tokio</a></h3>
        <p>Lock-free is_cancelled via AtomicBool, fixing >2ms mutex delays in hot paths</p>
      </div>

      <div class="oss-item-detailed">
        <h3><a href="https://github.com/hyperium/hyper/pull/4011">hyperium/hyper</a></h3>
        <p>Case-insensitive trailer field matching per RFC 9110 spec compliance</p>
      </div>

      <a href="#all-oss" class="expand-link">+ 6 more contributions</a>
    </section>
  </div>

  <!-- Right Column: Sidebar -->
  <div class="sidebar">
    <section class="sidebar-section">
      <h3>currently</h3>
      <p>Focused on Go for cloud infrastructure and networking. Contributing to Cilium eBPF. Exploring hardware projects.</p>
    </section>

    <section class="sidebar-section">
      <h3>interests</h3>
      <ul class="interest-list">
        <li>Distributed systems</li>
        <li>Cloud networking</li>
        <li>eBPF and kernel</li>
        <li>Real-time systems</li>
        <li>Performance optimization</li>
      </ul>
    </section>

    <section class="sidebar-section">
      <h3>recent posts</h3>
      <ul class="post-list">
        <li><a href="/2026/02/16/huffman-cpp.html">Huffman-Cpp</a></li>
        <li><a href="/2026/01/31/go-hunter.html">Go-Hunter</a></li>
        <li><a href="/blog">all posts →</a></li>
      </ul>
    </section>

    <section class="sidebar-section">
      <h3>outside work</h3>
      <p>Mountains, grappling, ultra marathons</p>
    </section>
  </div>
</div>

<!--
OPTION 2: "Minimal Two-Column"
- Clean, professional layout
- Two-column: main content (projects) + sidebar (info)
- Detailed project descriptions with tech stacks
- Expandable sections to see all projects
- Sidebar with current focus, interests, recent posts
-->
