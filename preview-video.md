---
layout: default
title: home
---

<div class="portfolio-layout">
  <!-- Header -->
  <header class="portfolio-header">
    <h1>Hugh</h1>
    <p class="intro">Go developer building cloud infrastructure and distributed systems. Contributing to production open source like Cilium eBPF.</p>
    <p class="location">Seattle · <a href="mailto:huecodes@proton.me">huecodes@proton.me</a> · <a href="https://github.com/HueCodes">GitHub</a></p>
  </header>

  <!-- Projects -->
  <section class="projects">
    <h2>Projects</h2>

    <!-- Project 1: Go-Hunter -->
    <article class="project">
      <img src="/assets/images/projects/go-hunter-dashboard.png" alt="Go-Hunter Dashboard" class="project-media">
      <h3><a href="https://github.com/HueCodes/Go-Hunter">Go-Hunter</a></h3>
      <p>Multi-cloud attack surface management platform for discovering and monitoring assets across AWS, GCP, Azure, Cloudflare, and DigitalOcean.</p>
      <ul>
        <li>Concurrent discovery across 5 cloud providers at 1,000 assets per minute using goroutine worker pools</li>
        <li>SHA256-based drift detection with hash comparison for tracking configuration changes</li>
        <li>Multi-tenant SaaS architecture with encrypted credential storage using age library</li>
        <li>Rate limiting per provider with exponential backoff to avoid API throttling</li>
      </ul>
      <p class="skills">`Go` `PostgreSQL` `Redis` `Asynq` `AWS SDK` `GCP API` `Azure SDK` `Chi Router`</p>
      <p class="links"><a href="/2026/01/31/go-hunter.html">Read more →</a></p>
    </article>

    <!-- Project 2: Forge-DB -->
    <article class="project">
      <img src="/assets/images/projects/forge-db-architecture.png" alt="Forge-DB Architecture" class="project-media">
      <h3><a href="https://github.com/HueCodes/forge-db">Forge-DB</a></h3>
      <p>SIMD-accelerated vector database with IVF-PQ indexing and HNSW graph search for high-performance similarity queries.</p>
      <ul>
        <li>AVX2/AVX-512 runtime detection for automatic SIMD optimization on different CPU architectures</li>
        <li>13,000 queries per second at 75 microsecond latency with product quantization compression</li>
        <li>32x memory compression using 8-bit quantization while maintaining 95%+ recall accuracy</li>
        <li>Pure Rust implementation with zero external dependencies, no_std compatible</li>
      </ul>
      <p class="skills">`Rust` `SIMD` `AVX2` `AVX-512` `HNSW` `Product Quantization` `no_std`</p>
      <p class="links"><a href="/2026/01/31/forge-db.html">Read more →</a></p>
    </article>

    <!-- Project 3: Nodix -->
    <article class="project">
      <img src="/assets/images/projects/nodix-sensor-fusion.png" alt="Nodix Sensor Fusion" class="project-media">
      <h3><a href="https://github.com/HueCodes/nodix">Nodix</a></h3>
      <p>Real-time compute graph engine for robotics with deterministic DAG execution and deadline-aware scheduling.</p>
      <ul>
        <li>Implemented EDF (Earliest Deadline First) and Rate Monotonic scheduling algorithms for real-time guarantees</li>
        <li>5,000+ iterations per second with sub-1ms p99 latency for sensor fusion pipelines</li>
        <li>Zero-copy data flow between nodes using Arc pointers and lock-free channels</li>
        <li>Topological sort validation for DAG cycles and dependency resolution</li>
      </ul>
      <p class="skills">`Rust` `Real-time Systems` `DAG` `EDF Scheduling` `Lock-free` `Arc` `Tokio`</p>
      <p class="links"><a href="/2026/01/31/nodix.html">Read more →</a></p>
    </article>

    <!-- Project 4: Rust ServiceMesh -->
    <article class="project">
      <img src="/assets/images/projects/servicemesh-architecture.png" alt="ServiceMesh Architecture" class="project-media">
      <h3><a href="https://github.com/HueCodes/Rust-ServiceMesh">Rust-ServiceMesh</a></h3>
      <p>Layer 7 proxy with circuit breakers, load balancing, and observability for microservices architectures.</p>
      <ul>
        <li>HTTP/2 and gRPC protocol support with full streaming capabilities using Hyper and Tonic</li>
        <li>Lock-free atomic operations with DashMap achieving 52 million operations per second</li>
        <li>Circuit breaker implementation with configurable failure thresholds and exponential backoff</li>
        <li>Prometheus metrics export, graceful shutdown handling, and connection pooling</li>
      </ul>
      <p class="skills">`Rust` `Tokio` `Hyper` `gRPC` `HTTP/2` `DashMap` `Prometheus` `Circuit Breaker`</p>
    </article>

    <!-- Project 5: Gretun -->
    <article class="project">
      <img src="/assets/images/projects/gretun-tunnel.png" alt="Gretun Tunnel" class="project-media">
      <h3><a href="https://github.com/HueCodes/gretun">Gretun</a></h3>
      <p>GRE tunnel management CLI for creating and managing site-to-site VPN tunnels between cloud VPCs.</p>
      <ul>
        <li>Direct netlink integration for kernel-level network configuration without external dependencies</li>
        <li>Tunnel creation, deletion, and health probing with ICMP echo requests</li>
        <li>Multi-cloud VPC interconnection for AWS, GCP, and Azure virtual networks</li>
        <li>Route table manipulation for steering traffic through GRE tunnels</li>
      </ul>
      <p class="skills">`Go` `Netlink` `GRE` `Linux Kernel` `VPN` `Networking` `Cloud VPC`</p>
      <p class="links"><a href="/2026/01/31/gretun.html">Read more →</a></p>
    </article>

    <!-- Project 6: Sensor-Bridge -->
    <article class="project">
      <img src="/assets/images/projects/sensor-bridge-pipeline.png" alt="Sensor Bridge Pipeline" class="project-media">
      <h3><a href="https://github.com/HueCodes/Sensor-Bridge">Sensor-Bridge</a></h3>
      <p>Lock-free 4-stage pipeline for IMU and LIDAR sensor fusion with sub-nanosecond latency.</p>
      <ul>
        <li>Cache-padded SPSC (Single Producer Single Consumer) ring buffers to avoid false sharing</li>
        <li>2.2 billion items per second throughput with approximately 20 nanosecond end-to-end latency</li>
        <li>Sub-nanosecond ring buffer operations using atomic operations without CAS loops</li>
        <li>no_std compatible for embedded systems and bare-metal environments</li>
      </ul>
      <p class="skills">`Rust` `Lock-free` `SPSC` `Ring Buffer` `Atomics` `IMU` `LIDAR` `no_std`</p>
    </article>

    <!-- Project 7: Huffman-Cpp -->
    <article class="project">
      <img src="/assets/images/projects/huffman-tree.svg" alt="Huffman Tree" class="project-media">
      <h3><a href="https://github.com/HueCodes/Compression-Algo-Huffman">Huffman-Cpp</a></h3>
      <p>Modern C++17 implementation of Huffman coding algorithm demonstrating priority queue-based tree construction and smart pointer memory management.</p>
      <ul>
        <li>Priority queue with custom comparator for greedy tree construction with O(n log k) complexity</li>
        <li>Smart pointers (unique_ptr) throughout for RAII and automatic memory management</li>
        <li>17 comprehensive unit tests covering edge cases like single-character inputs and prefix-free property validation</li>
        <li>Move semantics and string_view parameters to eliminate unnecessary allocations</li>
      </ul>
      <p class="skills">`C++17` `Smart Pointers` `Priority Queue` `RAII` `Algorithms` `Unit Testing`</p>
      <p class="links"><a href="/2026/02/16/huffman-cpp.html">Read more →</a></p>
    </article>
  </section>

  <!-- Open Source -->
  <section class="open-source">
    <h2>Open Source Contributions</h2>
    <ul class="contributions">
      <li><a href="https://github.com/cilium/ebpf/pull/1945"><strong>cilium/ebpf</strong></a> - Add poller and eventRing interfaces for eBPF event handling</li>
      <li><a href="https://github.com/tokio-rs/tokio/pull/7874"><strong>tokio</strong></a> - Lock-free is_cancelled via AtomicBool, fixing >2ms mutex delays in hot paths</li>
      <li><a href="https://github.com/hyperium/hyper/pull/4011"><strong>hyper</strong></a> - Case-insensitive trailer field matching per RFC 9110 spec compliance</li>
      <li><a href="https://github.com/rust-lang/rust-clippy/pull/16402"><strong>rust-clippy</strong></a> - Fixed false positive lint for proc-macro generated code</li>
      <li><a href="https://github.com/dimforge/rapier/pull/806"><strong>rapier</strong></a> - GeometricMean coefficient combine rule for friction simulation</li>
      <li><a href="https://github.com/RustPython/RustPython/pull/6661"><strong>rustpython</strong></a> - Fixed set in-place operators with self argument</li>
    </ul>
  </section>

  <!-- Footer -->
  <footer class="portfolio-footer">
    <p>Outside of engineering: mountains, grappling, ultra marathons</p>
    <p><a href="/blog">Writing</a> · <a href="/research">Research</a> · <a href="/hardware">Hardware</a></p>
  </footer>
</div>

<style>
.portfolio-layout {
  max-width: 900px;
  margin: 0 auto;
  padding: 60px 30px;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
  line-height: 1.7;
}

.portfolio-header {
  margin-bottom: 60px;
}

.portfolio-header h1 {
  font-size: 42px;
  font-weight: 700;
  margin: 0 0 15px 0;
}

.portfolio-header .intro {
  font-size: 18px;
  color: #333;
  margin: 0 0 10px 0;
}

.portfolio-header .location {
  color: #666;
  font-size: 15px;
  margin: 0;
}

.portfolio-header a {
  color: #0066cc;
  text-decoration: none;
}

.portfolio-header a:hover {
  text-decoration: underline;
}

.projects h2,
.open-source h2 {
  font-size: 28px;
  font-weight: 700;
  margin: 60px 0 30px 0;
  border-bottom: 2px solid #eee;
  padding-bottom: 10px;
}

.project {
  margin-bottom: 60px;
  padding-bottom: 40px;
  border-bottom: 1px solid #f0f0f0;
}

.project:last-child {
  border-bottom: none;
}

.project-media {
  width: 100%;
  height: auto;
  border-radius: 8px;
  margin-bottom: 20px;
  border: 1px solid #e0e0e0;
}

.project h3 {
  font-size: 24px;
  font-weight: 600;
  margin: 0 0 15px 0;
}

.project h3 a {
  color: #000;
  text-decoration: none;
}

.project h3 a:hover {
  color: #0066cc;
}

.project > p {
  color: #333;
  margin: 0 0 15px 0;
  font-size: 16px;
}

.project ul {
  margin: 0 0 15px 0;
  padding-left: 20px;
  color: #555;
}

.project ul li {
  margin-bottom: 8px;
  font-size: 15px;
}

.project .skills {
  font-family: 'Courier New', monospace;
  font-size: 13px;
  color: #666;
  background: #f8f8f8;
  padding: 10px 12px;
  border-radius: 4px;
  margin: 15px 0;
}

.project .links {
  margin: 15px 0 0 0;
}

.project .links a {
  color: #0066cc;
  text-decoration: none;
  font-size: 15px;
}

.project .links a:hover {
  text-decoration: underline;
}

.open-source {
  margin-top: 60px;
}

.contributions {
  list-style: none;
  padding: 0;
  margin: 0;
}

.contributions li {
  margin-bottom: 12px;
  font-size: 15px;
  color: #555;
}

.contributions a {
  color: #0066cc;
  text-decoration: none;
}

.contributions a:hover {
  text-decoration: underline;
}

.portfolio-footer {
  margin-top: 80px;
  padding-top: 30px;
  border-top: 1px solid #eee;
  text-align: center;
  color: #666;
  font-size: 14px;
}

.portfolio-footer a {
  color: #0066cc;
  text-decoration: none;
}

.portfolio-footer a:hover {
  text-decoration: underline;
}

@media (max-width: 768px) {
  .portfolio-layout {
    padding: 40px 20px;
  }

  .portfolio-header h1 {
    font-size: 32px;
  }

  .project h3 {
    font-size: 20px;
  }
}
</style>
