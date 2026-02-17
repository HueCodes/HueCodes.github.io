---
layout: grid
title: home
---

<div class="grid-portfolio">
  <!-- Header -->
  <header class="header">
    <h1>Hugh</h1>
    <p>Go · Cloud Infrastructure · Distributed Systems · Seattle</p>
    <nav>
      <a href="/blog">writing</a>
      <a href="https://github.com/HueCodes">github</a>
      <a href="mailto:huecodes@proton.me">email</a>
    </nav>
  </header>

  <!-- Projects Grid -->
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

  <!-- Open Source -->
  <section class="oss">
    <h2>Open Source</h2>
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
      <div class="oss-item">
        <a href="https://github.com/rust-lang/rust-clippy/pull/16402"><strong>clippy</strong></a>
        <p>Proc-macro lint fix</p>
      </div>
      <div class="oss-item">
        <a href="https://github.com/dimforge/rapier/pull/806"><strong>rapier</strong></a>
        <p>Friction simulation algorithm</p>
      </div>
      <div class="oss-item">
        <a href="https://github.com/RustPython/RustPython/pull/6661"><strong>rustpython</strong></a>
        <p>Set operators bugfix</p>
      </div>
    </div>
  </section>

  <footer>
    <p><a href="/blog">Writing</a> · <a href="/research">Research</a> · <a href="/hardware">Hardware</a></p>
  </footer>
</div>
