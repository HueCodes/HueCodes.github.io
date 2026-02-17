---
layout: grid
title: home
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/research">research</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>Go developer building cloud infrastructure and distributed systems.</p>
      <p>outside of engineering i like being in the mountains, grappling, and running ultra marathons. based in seattle.</p>
      <p class="tagline">i ship fast and want to solve hard problems with smart people.</p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section">
  <h2>currently</h2>
  <p>focused on systems programming and cloud infrastructure with Go and Rust. contributing to production open source projects like Cilium eBPF, tokio, and hyper. building cloud networking tools and exploring distributed systems patterns. also building Doppler radar and custom STM32 flight controller.</p>
</div>

<hr class="divider">

<div class="grid-portfolio">

  <!-- Projects -->
  <section class="projects-grid">
    <!-- Go-Hunter -->
    <article class="project-card">
      <img src="/assets/images/projects/go-hunter-dashboard.png" alt="Go-Hunter">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Go-Hunter">Go-Hunter</a></h3>
        <p class="tagline">Multi-cloud attack surface management</p>
        <ul>
          <li><strong>Scale:</strong> 1,000 assets/min across 5 cloud providers (AWS, GCP, Azure, Cloudflare, DO)</li>
          <li><strong>Architecture:</strong> Multi-tenant SaaS with encrypted credential storage and audit logging</li>
          <li><strong>Performance:</strong> Concurrent goroutine workers with per-provider rate limiting</li>
          <li><strong>Security:</strong> SHA256 drift detection, age encryption, row-level tenant isolation</li>
        </ul>
        <p class="tech">`Go` `PostgreSQL` `Redis` `AWS` `GCP` `Azure`</p>
      </div>
    </article>

    <!-- Forge-DB -->
    <article class="project-card">
      <img src="/assets/images/projects/forge-db-architecture.png" alt="Forge-DB">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/forge-db">Forge-DB</a></h3>
        <p class="tagline">SIMD-accelerated vector database</p>
        <ul>
          <li><strong>Performance:</strong> 13K QPS at 75µs latency with AVX2/AVX-512 optimization</li>
          <li><strong>Efficiency:</strong> 32x memory compression via 8-bit quantization, 95%+ recall</li>
          <li><strong>Portability:</strong> Pure Rust, zero external dependencies, no_std compatible</li>
          <li><strong>Algorithms:</strong> IVF-PQ indexing + HNSW graph search for similarity queries</li>
        </ul>
        <p class="tech">`Rust` `SIMD` `AVX-512` `HNSW` `Vector DB`</p>
      </div>
    </article>

    <!-- Nodix -->
    <article class="project-card">
      <img src="/assets/images/projects/nodix-sensor-fusion.png" alt="Nodix">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/nodix">Nodix</a></h3>
        <p class="tagline">Real-time compute graph for robotics</p>
        <ul>
          <li><strong>Real-time:</strong> <1ms p99 latency with EDF/Rate Monotonic scheduling</li>
          <li><strong>Throughput:</strong> 5,000+ iterations/sec for sensor fusion pipelines</li>
          <li><strong>Concurrency:</strong> Zero-copy data flow using Arc and lock-free channels</li>
          <li><strong>Correctness:</strong> DAG validation with topological sort and cycle detection</li>
        </ul>
        <p class="tech">`Rust` `Real-time` `DAG` `Lock-free` `Robotics`</p>
      </div>
    </article>

    <!-- ServiceMesh -->
    <article class="project-card">
      <img src="/assets/images/projects/servicemesh-architecture.png" alt="ServiceMesh">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Rust-ServiceMesh">ServiceMesh</a></h3>
        <p class="tagline">L7 proxy with circuit breakers</p>
        <ul>
          <li><strong>Throughput:</strong> 52M ops/sec using lock-free atomics + DashMap</li>
          <li><strong>Protocols:</strong> HTTP/2 and gRPC with full streaming support (Hyper/Tonic)</li>
          <li><strong>Reliability:</strong> Circuit breaker with configurable thresholds, exponential backoff</li>
          <li><strong>Observability:</strong> Prometheus metrics, graceful shutdown, connection pooling</li>
        </ul>
        <p class="tech">`Rust` `Tokio` `gRPC` `HTTP/2` `Prometheus`</p>
      </div>
    </article>

    <!-- Gretun -->
    <article class="project-card">
      <img src="/assets/images/projects/gretun-tunnel.png" alt="Gretun">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/gretun">Gretun</a></h3>
        <p class="tagline">GRE tunnel management CLI</p>
        <ul>
          <li><strong>Networking:</strong> Site-to-site VPN tunnels for cloud VPC interconnection</li>
          <li><strong>Low-level:</strong> Direct netlink integration for kernel network configuration</li>
          <li><strong>Features:</strong> Tunnel creation, health probing (ICMP), route table manipulation</li>
          <li><strong>Cloud-native:</strong> Multi-cloud support (AWS, GCP, Azure virtual networks)</li>
        </ul>
        <p class="tech">`Go` `Netlink` `GRE` `VPN` `Cloud Networking`</p>
      </div>
    </article>

    <!-- Sensor-Bridge -->
    <article class="project-card">
      <img src="/assets/images/projects/sensor-bridge-pipeline.png" alt="Sensor-Bridge">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Sensor-Bridge">Sensor-Bridge</a></h3>
        <p class="tagline">Lock-free sensor fusion pipeline</p>
        <ul>
          <li><strong>Throughput:</strong> 2.2B items/sec with ~20ns end-to-end latency</li>
          <li><strong>Optimization:</strong> Cache-padded SPSC buffers to avoid false sharing</li>
          <li><strong>Lock-free:</strong> Sub-nanosecond ring buffer ops using atomics (no CAS loops)</li>
          <li><strong>Embedded:</strong> no_std compatible for bare-metal environments</li>
        </ul>
        <p class="tech">`Rust` `Lock-free` `SPSC` `Atomics` `Embedded`</p>
      </div>
    </article>

    <!-- Huffman-Cpp -->
    <article class="project-card">
      <img src="/assets/images/projects/huffman-tree.svg" alt="Huffman-Cpp">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Compression-Algo-Huffman">Huffman-Cpp</a></h3>
        <p class="tagline">Modern C++17 compression algorithm</p>
        <ul>
          <li><strong>Modern C++:</strong> Smart pointers (unique_ptr), RAII, move semantics, string_view</li>
          <li><strong>Algorithms:</strong> Priority queue with O(n log k) complexity, greedy tree construction</li>
          <li><strong>Testing:</strong> 17 comprehensive unit tests, 100% pass rate, edge case coverage</li>
          <li><strong>Best practices:</strong> Const-correctness, noexcept, [[nodiscard]] attributes</li>
        </ul>
        <p class="tech">`C++17` `Smart Pointers` `Priority Queue` `RAII`</p>
      </div>
    </article>
  </section>

  <!-- Open Source Highlights -->
  <section class="oss">
    <h2>Open Source Highlights</h2>
    <div class="oss-grid">
      <div class="oss-item">
        <a href="https://github.com/cilium/ebpf/pull/1945"><strong>cilium/ebpf</strong></a>
        <p>Poller and eventRing interfaces</p>
      </div>
      <div class="oss-item">
        <a href="https://github.com/tokio-rs/tokio/pull/7874"><strong>tokio</strong></a>
        <p>Lock-free cancellation (>2ms speedup)</p>
      </div>
      <div class="oss-item">
        <a href="https://github.com/hyperium/hyper/pull/4011"><strong>hyper</strong></a>
        <p>RFC 9110 trailer compliance</p>
      </div>
    </div>
    <p style="text-align: center; margin-top: 20px;">
      <a href="/opensource" style="color: #0066cc; text-decoration: none; font-size: 14px;">View all contributions →</a>
    </p>
  </section>

  <footer>
    <p><a href="/blog">Writing</a> · <a href="/research">Research</a> · <a href="/hardware">Hardware</a></p>
  </footer>
</div>
