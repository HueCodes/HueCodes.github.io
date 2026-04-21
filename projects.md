---
layout: grid
title: projects
---

<nav>
  <a href="/">home</a>
  <a href="/projects">projects</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="grid-portfolio">

  <div class="filter-tabs">
    <button class="filter-btn active" data-filter="software">software</button>
    <button class="filter-btn" data-filter="hardware">hardware</button>
  </div>

  <!-- Projects -->
  <section class="projects-grid">
    <!-- Moat — uncomment when repo goes public
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/moat-architecture.svg" alt="Moat">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Moat">Moat</a></h3>
        <p class="tagline">mTLS for AI agents</p>
        <ul>
          <li><strong>Identity:</strong> Ed25519 per-agent keys; every message signed with monotonic sequence numbers for replay protection</li>
          <li><strong>Capability tokens:</strong> Monotonic attenuation enforced by construction — children can only narrow scope, never broaden</li>
          <li><strong>Sandbox:</strong> wasmtime + WASI with fuel limits, memory caps, default-deny filesystem and network</li>
          <li><strong>Audit:</strong> Append-only SHA-256 hash chain — any retroactive mutation breaks all subsequent hashes</li>
        </ul>
        <div class="tech"><span>Rust</span><span>Ed25519</span><span>wasmtime</span><span>WASI</span><span>Tokio</span></div>
        <p class="blog-link"><span style="color: var(--accent); opacity: 0.6;">coming soon</span></p>
      </div>
    </article>
    -->

    <!-- Archimedes -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/archimedes-critical-area.png" alt="Archimedes">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Archimedes">Archimedes</a></h3>
        <p class="tagline">Interactive computational geometry in Rust + WASM + WebGPU</p>
        <ul>
          <li><strong>Algorithms:</strong> Andrew's monotone-chain convex hull, Bowyer-Watson Delaunay triangulation, polygon boolean ops via i_overlay</li>
          <li><strong>Semiconductor:</strong> Critical-area analysis — Minkowski-style dilation for VLSI yield prediction (Papadopoulou & Lee, 1999)</li>
          <li><strong>Robustness:</strong> Side-by-side naive f32 vs Shewchuk adaptive-precision predicates on near-degenerate input</li>
          <li><strong>Stack:</strong> egui + wgpu rendering, trunk for wasm build, runs in-browser via WebAssembly</li>
        </ul>
        <div class="tech"><span>Rust</span><span>WebAssembly</span><span>WebGPU</span><span>egui</span><span>Computational Geometry</span></div>
        <p class="blog-link"><a href="https://huecodes.github.io/Archimedes/">→ live demo</a></p>
      </div>
    </article>

    <!-- Gretun -->
    <article class="project-card" data-category="software">
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
        <div class="tech"><span>Go</span><span>Netlink</span><span>GRE</span><span>VPN</span><span>Cloud Networking</span></div>
        <p class="blog-link"><a href="/projects/gretun/">→ read more</a></p>
      </div>
    </article>

    <!-- Sensor-Bridge -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/sensor-bridge-demo.gif" alt="Sensor-Bridge">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Sensor-Bridge">Sensor-Bridge</a></h3>
        <p class="tagline">Lock-free sensor fusion pipeline</p>
        <ul>
          <li><strong>Throughput:</strong> 2.2B items/sec with ~20ns end-to-end latency</li>
          <li><strong>Optimization:</strong> Cache-padded SPSC buffers to avoid false sharing</li>
          <li><strong>Lock-free:</strong> Sub-nanosecond ring buffer ops using atomics (no CAS loops)</li>
          <li><strong>Embedded:</strong> no_std compatible for bare-metal environments</li>
        </ul>
        <div class="tech"><span>Rust</span><span>Lock-free</span><span>SPSC</span><span>Atomics</span><span>Embedded</span></div>
        <p class="blog-link"><a href="/projects/sensor-bridge/">→ read more</a></p>
      </div>
    </article>

    <!-- ServiceMesh -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/servicemesh-demo.gif" alt="ServiceMesh">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Rust-ServiceMesh">ServiceMesh</a></h3>
        <p class="tagline">L7 proxy with circuit breakers</p>
        <ul>
          <li><strong>Throughput:</strong> 52M ops/sec using lock-free atomics + DashMap</li>
          <li><strong>Protocols:</strong> HTTP/2 and gRPC with full streaming support (Hyper/Tonic)</li>
          <li><strong>Reliability:</strong> Circuit breaker with configurable thresholds, exponential backoff</li>
          <li><strong>Observability:</strong> Prometheus metrics, graceful shutdown, connection pooling</li>
        </ul>
        <div class="tech"><span>Rust</span><span>Tokio</span><span>gRPC</span><span>HTTP/2</span><span>Prometheus</span></div>
        <p class="blog-link"><a href="/projects/rust-servicemesh/">→ read more</a></p>
      </div>
    </article>

    <!-- Container-runtime-rs -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/container-runtime-architecture.svg" alt="Container-runtime-rs">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Container-runtime-rs">Container-runtime-rs</a></h3>
        <p class="tagline">OCI-compliant container runtime in Rust</p>
        <ul>
          <li><strong>Isolation:</strong> Linux namespaces (PID/mount/UTS/IPC/net), cgroups v2 with memory/CPU/PID limits</li>
          <li><strong>Storage:</strong> OverlayFS for layered images, OCI image pulling and layer extraction</li>
          <li><strong>Security:</strong> Seccomp BPF filters, capability dropping, no_new_privs, user namespaces</li>
          <li><strong>Architecture:</strong> 900+ source files, full OCI runtime spec compliance, Docker-comparable lifecycle</li>
        </ul>
        <div class="tech"><span>Rust</span><span>Linux Namespaces</span><span>cgroups v2</span><span>OverlayFS</span><span>seccomp</span><span>OCI</span></div>
        <p class="blog-link"><a href="https://github.com/HueCodes/Container-runtime-rs">→ github</a></p>
      </div>
    </article>

    <!-- Nodix -->
    <article class="project-card" data-category="software">
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
        <div class="tech"><span>Rust</span><span>Real-time</span><span>DAG</span><span>Lock-free</span><span>Robotics</span></div>
        <p class="blog-link"><a href="/projects/nodix/">→ read more</a></p>
      </div>
    </article>

    <!-- Network-Beacon -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/network-beacon-architecture.svg" alt="Network-Beacon">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Network-Beacon">Network-Beacon</a></h3>
        <p class="tagline">C2 beacon detection via traffic analysis and TLS fingerprinting</p>
        <ul>
          <li><strong>Detection:</strong> CV-based jitter analysis, JA4 TLS fingerprinting, DNS tunneling, HTTP beaconing patterns</li>
          <li><strong>Pipeline:</strong> Multi-threaded async (Tokio) packet processing with libpcap; live capture and PCAP replay</li>
          <li><strong>Output:</strong> Ratatui TUI dashboard, webhook + syslog alerting, Prometheus metrics, JSON/JSONL for SIEM</li>
          <li><strong>Configuration:</strong> TOML profiles (paranoid/balanced/relaxed), GeoIP enrichment via MaxMind</li>
        </ul>
        <div class="tech"><span>Rust</span><span>Tokio</span><span>libpcap</span><span>Ratatui</span><span>JA4</span><span>Prometheus</span></div>
        <p class="blog-link"><a href="https://github.com/HueCodes/Network-Beacon">→ github</a></p>
      </div>
    </article>

    <!-- Keysmith -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/keysmith-operator.svg" alt="Keysmith">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/keysmith">keysmith</a></h3>
        <p class="tagline">Kubernetes operator for automated secret rotation</p>
        <ul>
          <li><strong>Operator:</strong> SecretRotationPolicy CRD reconciled by controller-runtime; immutable RotationRecord audit trail per attempt</li>
          <li><strong>GitOps:</strong> declarative rotation policies live next to workload manifests; Kustomize overlays + single-manifest deploy</li>
          <li><strong>Providers:</strong> pluggable interface — AWS Secrets Manager, HashiCorp Vault, built-in crypto/rand generator</li>
          <li><strong>Observability:</strong> Prometheus metrics, OpenTelemetry tracing, rolling restarts of Deployments/StatefulSets/DaemonSets</li>
        </ul>
        <div class="tech"><span>Go</span><span>Kubernetes</span><span>controller-runtime</span><span>CRDs</span><span>Prometheus</span><span>OpenTelemetry</span></div>
        <p class="blog-link"><a href="/projects/keysmith/">→ read more</a></p>
      </div>
    </article>

    <!-- Sentinel -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/sentinel-pipeline.svg" alt="Sentinel">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/sentinel">Sentinel</a></h3>
        <p class="tagline">Embedded SIEM log pipeline</p>
        <ul>
          <li><strong>Ingest:</strong> Suricata EVE, Cowrie honeypot, C2 beacons, syslog (RFC 5424/3164), arbitrary JSON</li>
          <li><strong>Enrichment:</strong> GeoIP lookups, threat intel feed matching, MITRE ATT&CK mapping</li>
          <li><strong>Detection:</strong> TOML-driven rule engine with correlation — reconstructs kill chains across sources</li>
          <li><strong>Output:</strong> Ratatui TUI dashboard, HMAC-signed webhooks, Prometheus metrics, REST API with CSV/JSON export</li>
        </ul>
        <div class="tech"><span>Rust</span><span>DuckDB</span><span>Tokio</span><span>Axum</span><span>Ratatui</span><span>Prometheus</span></div>
        <p class="blog-link"><a href="/projects/sentinel/">→ read more</a></p>
      </div>
    </article>

    <!-- UDP-Relay -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/udp-relay-arch.svg" alt="UDP-Relay">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/udp-relay">UDP-Relay</a></h3>
        <p class="tagline">MAVLink v2 telemetry aggregator</p>
        <ul>
          <li><strong>Ingest:</strong> UDP listener on :14550 with MAVLink v2 frame parsing and CRC-16/MCRF4XX validation</li>
          <li><strong>Registry:</strong> Thread-safe drone registry tracking position, attitude, battery, and flight mode for 250 vehicles</li>
          <li><strong>Streaming:</strong> Non-blocking pub/sub hub fans events to filtered WebSocket subscriptions</li>
          <li><strong>Ops:</strong> Prometheus metrics, token bucket rate limiting, IP/CIDR whitelisting, distroless Docker image</li>
        </ul>
        <div class="tech"><span>Go</span><span>MAVLink</span><span>WebSocket</span><span>Prometheus</span><span>Docker</span></div>
        <p class="blog-link"><a href="/projects/udp-relay/">→ read more</a></p>
      </div>
    </article>

    <!-- Go-Hunter -->
    <article class="project-card" data-category="software">
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
        <div class="tech"><span>Go</span><span>PostgreSQL</span><span>Redis</span><span>AWS</span><span>GCP</span><span>Azure</span></div>
        <p class="blog-link"><a href="/projects/go-hunter/">→ read more</a></p>
      </div>
    </article>

    <!-- Forge-DB -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/forge-db-demo.gif" alt="Forge-DB">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/forge-db">Forge-DB</a></h3>
        <p class="tagline">SIMD-accelerated vector database</p>
        <ul>
          <li><strong>Performance:</strong> 13K QPS at 75us latency with AVX2/AVX-512 optimization</li>
          <li><strong>Efficiency:</strong> 32x memory compression via 8-bit quantization, 95%+ recall</li>
          <li><strong>Portability:</strong> Pure Rust, zero external dependencies, no_std compatible</li>
          <li><strong>Algorithms:</strong> IVF-PQ indexing + HNSW graph search for similarity queries</li>
        </ul>
        <div class="tech"><span>Rust</span><span>SIMD</span><span>AVX-512</span><span>HNSW</span><span>Vector DB</span></div>
        <p class="blog-link"><a href="/projects/forge-db/">→ read more</a></p>
      </div>
    </article>

    <!-- PID Motor Control -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/pid-motor-control.svg" alt="PID Motor Control">
      <div class="card-content">
        <h3><a href="/hardware/pid-motor-control/">PID Motor Control</a></h3>
        <p class="tagline">Closed-loop PID controller on STM32F411RE with IMU feedback</p>
        <ul>
          <li><strong>Control:</strong> 1kHz PID loop in TIM2 ISR with derivative-on-measurement and integral anti-windup</li>
          <li><strong>Sensing:</strong> MPU-6050 IMU at 400kHz I2C, complementary filter (alpha=0.98)</li>
          <li><strong>Actuation:</strong> TB6612FNG motor driver, 20kHz PWM, sign-magnitude drive (-1.0 to +1.0)</li>
          <li><strong>Tuning:</strong> UART CLI for live Kp/Ki/Kd adjustment, step response CSV capture for plotting</li>
        </ul>
        <div class="tech"><span>STM32F411RE</span><span>PID</span><span>MPU-6050</span><span>TB6612FNG</span><span>CMake</span></div>
        <p class="blog-link"><a href="/hardware/pid-motor-control/">→ view project</a></p>
      </div>
    </article>

    <!-- FreeRTOS STM32 -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/freertos-stm32-tasks.svg" alt="FreeRTOS STM32">
      <div class="card-content">
        <h3><a href="/hardware/freertos-stm32/">FreeRTOS STM32</a></h3>
        <p class="tagline">5-task preemptive firmware on STM32F411RE @ 100MHz</p>
        <ul>
          <li><strong>Pipeline:</strong> Queue-based sensor → processing → UART TX with ISR-safe xQueueSendFromISR</li>
          <li><strong>Watchdog:</strong> Supervisor task (P=4) feeds IWDG only when all tasks check in — resets on hang</li>
          <li><strong>Persistence:</strong> Config struct in flash with CRC32 validation, safe fallback to defaults</li>
          <li><strong>CLI:</strong> UART shell (status, config get/set, task suspend) via ISR-fed rx_char_queue</li>
        </ul>
        <div class="tech"><span>FreeRTOS</span><span>STM32F411RE</span><span>CMake</span><span>BME280</span><span>IWDG</span></div>
        <p class="blog-link"><a href="/hardware/freertos-stm32/">→ view project</a></p>
      </div>
    </article>

  </section>
</div>

<script>
  document.querySelectorAll('.filter-btn').forEach(function(btn) {
    btn.addEventListener('click', function() {
      var category = this.dataset.filter;
      document.querySelectorAll('.project-card').forEach(function(card) {
        card.style.display = card.dataset.category === category ? '' : 'none';
      });
      document.querySelectorAll('.filter-btn').forEach(function(b) {
        b.classList.toggle('active', b.dataset.filter === category);
      });
    });
  });
  document.querySelector('.filter-btn[data-filter="software"]').click();
</script>
