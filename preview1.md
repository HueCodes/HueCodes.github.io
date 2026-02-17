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

<!-- Hero Section with Larger Profile -->
<div class="hero">
  <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="hero-photo">
  <h1>Hugh</h1>
  <p class="hero-tagline">Go developer building cloud infrastructure and distributed systems</p>
  <p class="hero-subtitle">I ship fast and want to solve hard problems with smart people</p>
  <div class="hero-stats">
    <div class="stat">
      <span class="stat-number">6</span>
      <span class="stat-label">projects</span>
    </div>
    <div class="stat">
      <span class="stat-number">9</span>
      <span class="stat-label">OSS contributions</span>
    </div>
    <div class="stat">
      <span class="stat-number">5</span>
      <span class="stat-label">blog posts</span>
    </div>
  </div>
</div>

<hr class="divider">

<!-- About (Combined with Currently) -->
<div class="section about-section">
  <h2>about</h2>
  <p><strong>Currently:</strong> Focused on Go development for cloud infrastructure and networking. Contributing to production open source projects like Cilium eBPF. Diving into hardware.</p>
  <p>Outside of computers and engineering I like being in the mountains, grappling, and running ultra marathons. Based in Seattle.</p>
</div>

<hr class="divider">

<!-- Featured Projects (Grid Layout) -->
<div class="section">
  <h2>featured projects</h2>
  <div class="projects-grid">
    <div class="project-card featured">
      <div class="project-header">
        <a href="https://github.com/HueCodes/Go-Hunter">go-hunter</a>
        <span class="project-tech">Go</span>
      </div>
      <p class="project-desc">Multi-cloud attack surface management. Concurrent discovery across AWS/GCP/Azure/Cloudflare/DO at 1K assets/min, SHA256-based drift detection, multi-tenant SaaS.</p>
      <div class="project-tags">
        <span class="tag">AWS</span>
        <span class="tag">GCP</span>
        <span class="tag">Security</span>
      </div>
    </div>

    <div class="project-card featured">
      <div class="project-header">
        <a href="https://github.com/HueCodes/forge-db">forge-db</a>
        <span class="project-tech">Rust</span>
      </div>
      <p class="project-desc">SIMD-accelerated vector database. IVF-PQ + HNSW indexing, AVX2/AVX-512 runtime detection, 13K QPS at 75µs, 32x memory compression. Pure Rust, no external deps.</p>
      <div class="project-tags">
        <span class="tag">SIMD</span>
        <span class="tag">Database</span>
        <span class="tag">Performance</span>
      </div>
    </div>

    <div class="project-card featured">
      <div class="project-header">
        <a href="https://github.com/HueCodes/nodix">nodix</a>
        <span class="project-tech">Rust</span>
      </div>
      <p class="project-desc">Real-time compute graph engine for robotics. DAG execution with EDF/Rate Monotonic scheduling, 5K+ iter/sec, <1ms p99 latency, zero-copy data flow.</p>
      <div class="project-tags">
        <span class="tag">Robotics</span>
        <span class="tag">Real-time</span>
        <span class="tag">Performance</span>
      </div>
    </div>
  </div>

  <details class="more-projects">
    <summary>more projects</summary>
    <div class="project-list">
      <div class="project">
        <a href="https://github.com/HueCodes/gretun">gretun</a>
        <span>GRE tunnel management CLI. Create, probe, and manage site-to-site tunnels for cloud VPC interconnection via netlink. Go</span>
      </div>
      <div class="project">
        <a href="https://github.com/HueCodes/Rust-ServiceMesh">rust-servicemesh</a>
        <span>L7 proxy with circuit breakers, HTTP/2, gRPC. Lock-free atomics + DashMap achieve 52M ops/sec. Prometheus metrics, graceful shutdown, connection pooling. Rust</span>
      </div>
      <div class="project">
        <a href="https://github.com/HueCodes/Sensor-Bridge">sensor-bridge</a>
        <span>Lock-free 4-stage pipeline for IMU/LIDAR fusion. Cache-padded SPSC buffers, 2.2B items/sec, ~20ns latency, sub-nanosecond ring buffer ops. no_std compatible. Rust</span>
      </div>
      <div class="project">
        <a href="https://github.com/HueCodes/Compression-Algo-Huffman">huffman-cpp</a>
        <span>Modern C++17 Huffman coding implementation. Smart pointers, priority queue-based tree construction, 17 comprehensive unit tests. Educational compression algorithm. C++</span>
      </div>
    </div>
  </details>
</div>

<hr class="divider">

<!-- Recent Blog Posts -->
<div class="section">
  <h2>recent writing</h2>
  <div class="blog-preview">
    <div class="blog-item">
      <a href="/2026/02/16/huffman-cpp.html">Huffman-Cpp: Modern C++ Implementation</a>
      <span class="date">Feb 16, 2026</span>
    </div>
    <div class="blog-item">
      <a href="/2026/01/31/go-hunter.html">Go-Hunter: Multi-Cloud Attack Surface Management</a>
      <span class="date">Jan 31, 2026</span>
    </div>
  </div>
  <a href="/blog" class="see-all">see all posts →</a>
</div>

<hr class="divider">

<!-- Open Source Contributions -->
<div class="section">
  <h2>open source</h2>
  <div class="oss-highlights">
    <div class="oss-item">
      <a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf</a>
      <span>Add poller and eventRing interfaces</span>
    </div>
    <div class="oss-item">
      <a href="https://github.com/tokio-rs/tokio/pull/7874">tokio</a>
      <span>Lock-free is_cancelled via AtomicBool for CancellationToken, fixing >2ms mutex delays</span>
    </div>
    <div class="oss-item">
      <a href="https://github.com/hyperium/hyper/pull/4011">hyper</a>
      <span>Case-insensitive trailer field matching per RFC 9110</span>
    </div>
  </div>
  <details class="more-oss">
    <summary>more contributions</summary>
    <div class="project-list">
      <div class="project">
        <a href="https://github.com/rust-lang/rust-clippy/pull/16402">rust-clippy</a>
        <span>Fixed false positive lint for proc-macro generated code</span>
      </div>
      <div class="project">
        <a href="https://github.com/cloudflare/agents/pull/781">cloudflare/agents</a>
        <span>Fix: properly type tool error content in getAITools</span>
      </div>
      <div class="project">
        <a href="https://github.com/dimforge/rapier/pull/806">rapier</a>
        <span>GeometricMean coefficient combine rule for friction simulation</span>
      </div>
      <div class="project">
        <a href="https://github.com/RustPython/RustPython/pull/6661">rustpython</a>
        <span>Fixed set in-place operators with self argument</span>
      </div>
      <div class="project">
        <a href="https://github.com/lapce/floem/pull/1025">floem</a>
        <span>Cache whitespace TextLayouts in editor paint</span>
      </div>
      <div class="project">
        <a href="https://github.com/jeremyfix/torchcvnn/pull/47">torchcvnn</a>
        <span>Added input validation to normalization layers</span>
      </div>
    </div>
  </details>
</div>

<div class="section">
  <p class="links">
    <a href="mailto:huecodes@proton.me">email</a>
  </p>
</div>

<!--
OPTION 1: "Hero with Stats"
- Large hero section with photo and stats
- Featured projects in grid cards with tags
- Collapsible sections for more projects/contributions
- Recent blog posts preview
- Combined about/currently section
-->
