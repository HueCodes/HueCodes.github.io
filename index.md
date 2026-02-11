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

<div style="background: #f0f9ff; border-left: 4px solid #3b82f6; padding: 1rem; margin-bottom: 2rem; border-radius: 4px;">
  <p style="margin: 0; color: #1e40af; font-weight: 500;">
    Currently seeking <strong>junior Go developer</strong> opportunities in Seattle or remote.
    <a href="mailto:huecodes@proton.me" style="color: #3b82f6; text-decoration: underline;">Get in touch</a>
  </p>
</div>

<div class="intro">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>building distributed systems and cloud infrastructure in <strong>Go</strong> and Rust.</p>
      <p class="tagline">self-taught developer focused on networking, multi-cloud platforms, and infrastructure automation.</p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section">
  <h2>about</h2>
  <p>self-taught developer based in seattle, seeking junior Go developer roles. building cloud networking tools and multi-cloud platforms. strong foundation in Go, networking (TCP/UDP, HTTP/2, GRE tunnels, VPC design), and cloud infrastructure (AWS, GCP, Azure). contributor to <a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf</a> and other production systems.</p>
  <p>planning to pursue electrical engineering starting fall 2026. outside of code: ultra marathons, brazilian jiu-jitsu, and math.</p>
</div>

<div class="section">
  <h2>technical skills</h2>
  <p><strong>Go:</strong> net/http, goroutines/channels, context, netlink, database/sql, testing, Chi router, Asynq</p>
  <p><strong>Networking:</strong> TCP/UDP, HTTP/2, GRE tunnels, VPC networking, BGP fundamentals, eBPF basics</p>
  <p><strong>Cloud:</strong> AWS, GCP, Azure, Kubernetes, Docker, multi-cloud orchestration</p>
  <p><strong>Also:</strong> Rust (Tokio, Hyper), PostgreSQL, Redis, Prometheus, OpenTofu/Terraform</p>
</div>

<div class="section">
  <h2>currently</h2>
  <p>seeking junior Go developer roles. building cloud networking tools (<a href="https://github.com/HueCodes/gretun">gretun</a>) and multi-cloud platforms (<a href="https://github.com/HueCodes/Go-Hunter">Go-hunter</a>). contributing to cilium/ebpf, Hyper, Cloudflare agents. exploring hardware and embedded systems.</p>
</div>

<div class="section">
  <h2>go projects</h2>
  <div class="project">
    <a href="https://github.com/HueCodes/gretun">gretun</a>
    <span>GRE tunnel management CLI. create, probe, and manage site-to-site tunnels for cloud VPC interconnection via netlink. Go · <a href="/blog/gretun">read more</a></span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Go-Hunter">go-hunter</a>
    <span>multi-cloud attack surface management. concurrent discovery across AWS/GCP/Azure/Cloudflare/DO at 1K assets/min, SHA256-based drift detection, multi-tenant SaaS. Go · <a href="/blog/go-hunter">read more</a></span>
  </div>
</div>

<div class="section">
  <h2>other projects</h2>
  <div class="project">
    <a href="https://github.com/HueCodes/Sensor-Bridge">sensor-bridge</a>
    <span>lock-free 4-stage pipeline for IMU/LIDAR fusion. cache-padded SPSC buffers, 2.2B items/sec, ~20ns latency, sub-nanosecond ring buffer ops. no_std compatible. Rust · <a href="/blog/sensor-bridge">read more</a></span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/Rust-ServiceMesh">rust-servicemesh</a>
    <span>L7 proxy with circuit breakers, HTTP/2, gRPC. lock-free atomics + DashMap achieve 52M ops/sec. Prometheus metrics, graceful shutdown, connection pooling. Rust · <a href="/blog/rust-servicemesh">read more</a></span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/forge-db">forge-db</a>
    <span>SIMD-accelerated vector database. IVF-PQ + HNSW indexing, AVX2/AVX-512 runtime detection, 13K QPS at 75µs, 32x memory compression. pure Rust, no external deps. Rust · <a href="/blog/forge-db">read more</a></span>
  </div>
  <div class="project">
    <a href="https://github.com/HueCodes/nodix">nodix</a>
    <span>real-time compute graph engine for robotics. DAG execution with EDF/Rate Monotonic scheduling, 5K+ iter/sec, <1ms p99 latency, zero-copy data flow. Rust · <a href="/blog/nodix">read more</a></span>
  </div>
</div>

<div class="section">
  <h2>open source</h2>
  <div class="project">
    <a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf</a>
    <span>add poller and eventRing interfaces for cross-platform ringbuf support. Go</span>
  </div>
  <div class="project">
    <a href="https://github.com/hyperium/hyper/pull/4011">hyper</a>
    <span>case-insensitive trailer field matching per RFC 9110</span>
  </div>
  <div class="project">
    <a href="https://github.com/rust-lang/rust-clippy/pull/16402">rust-clippy</a>
    <span>fixed false positive lint for proc-macro generated code</span>
  </div>
  <div class="project">
    <a href="https://github.com/cloudflare/agents/pull/781">cloudflare/agents</a>
    <span>fix: properly type tool error content in getAITools</span>
  </div>
  <div class="project">
    <a href="https://github.com/dimforge/rapier/pull/806">rapier</a>
    <span>GeometricMean coefficient combine rule for friction simulation (open, passes checks)</span>
  </div>
  <div class="project">
    <a href="https://github.com/RustPython/RustPython/pull/6661">rustpython</a>
    <span>fixed set in-place operators with self argument</span>
  </div>
  <div class="project">
    <a href="https://github.com/lapce/floem/pull/1025">floem</a>
    <span>cache whitespace TextLayouts in editor paint</span>
  </div>
  <div class="project">
    <a href="https://github.com/tokio-rs/tokio/pull/7874">tokio</a>
    <span>lock-free is_cancelled via AtomicBool for CancellationToken, fixing >2ms mutex delays (open, passes checks)</span>
  </div>
  <div class="project">
    <a href="https://github.com/jeremyfix/torchcvnn/pull/47">torchcvnn</a>
    <span>added input validation to normalization layers</span>
  </div>
</div>

<div class="section">
  <p class="links">
    <a href="mailto:huecodes@proton.me">email</a>
  </p>
</div>
