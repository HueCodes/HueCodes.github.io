---
layout: grid
title: home
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="/research">research</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>building security tools, networking, cloud infrastructure, and systems software. diving into hardware and robotics.</p>
      <p>outside of engineering i like being in the mountains, grappling, and running ultra marathons. based in seattle.</p>
      <p class="tagline">i ship fast and want to solve hard problems with smart people.</p>
      <p><a href="mailto:huecodes@proton.me">huecodes@proton.me</a></p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section">
  <h2>currently</h2>
  <p>security research and detection engineering. building infrastructure tooling in Go and Rust. contributing to Cilium eBPF, Prometheus, SigmaHQ, and hyper. exploring hardware and robotics.</p>
</div>

<hr class="divider">

<div class="grid-portfolio">

  <div class="filter-tabs">
    <button class="filter-btn active" data-filter="software">software</button>
    <button class="filter-btn" data-filter="hardware">hardware</button>
  </div>

  <!-- Projects -->
  <section class="projects-grid">
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
        <p class="tech">`Go` `Kubernetes` `controller-runtime` `CRDs` `Prometheus` `OpenTelemetry`</p>
        <p class="blog-link"><a href="/blog/keysmith/">→ blog</a></p>
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
        <p class="tech">`Go` `PostgreSQL` `Redis` `AWS` `GCP` `Azure`</p>
        <p class="blog-link"><a href="/blog/go-hunter/">→ read more</a></p>
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
        <p class="tech">`Go` `Netlink` `GRE` `VPN` `Cloud Networking`</p>
        <p class="blog-link"><a href="/blog/gretun/">→ read more</a></p>
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
        <p class="tech">`Rust` `Real-time` `DAG` `Lock-free` `Robotics`</p>
        <p class="blog-link"><a href="/blog/nodix/">→ read more</a></p>
      </div>
    </article>

    <!-- Sensor-Bridge -->
    <article class="project-card" data-category="software">
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
        <p class="blog-link"><a href="/blog/sensor-bridge/">→ read more</a></p>
      </div>
    </article>

    <!-- Huffman-Cpp -->
    <article class="project-card" data-category="software">
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
        <p class="blog-link"><a href="/blog/huffman-cpp/">→ read more</a></p>
      </div>
    </article>

    <!-- Elastic Collision -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/elastic-collision-gpu.svg" alt="Elastic Collision Engine">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/Cpp-Particle-Sim">Elastic Collision Engine</a></h3>
        <p class="tagline">OpenCL GPU compute + spatial hashing for 1000-particle physics</p>
        <ul>
          <li><strong>GPU compute:</strong> OpenCL kernels for parallel collision resolution, one work item per particle</li>
          <li><strong>Spatial hashing:</strong> O(n) broad-phase detection via uniform grid, 9-cell neighborhood lookup</li>
          <li><strong>Physics:</strong> Elastic collision formula with mass-weighted positional correction</li>
          <li><strong>Architecture:</strong> CPU/GPU split — grid built CPU-side, collision resolution GPU-side</li>
        </ul>
        <p class="tech">`C++` `OpenCL` `SDL2` `Spatial Hashing` `Physics Simulation`</p>
        <p class="blog-link"><a href="/blog/elastic-collision-opencl/">→ read more</a></p>
      </div>
    </article>

    <!-- UART-Cpp -->
    <article class="project-card" data-category="software">
      <img src="/assets/images/projects/uart-cpp-system.svg" alt="UART-Cpp">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/UART-cpp">UART-Cpp</a></h3>
        <p class="tagline">Bare-metal UART driver for STM32F4</p>
        <ul>
          <li><strong>No HAL:</strong> Direct register access via CMSIS only, ~200 lines replacing 20,000+ of generated code</li>
          <li><strong>Interrupt-driven:</strong> Non-blocking TX via on-demand TXEIE, ISR-driven RX with ring buffers</li>
          <li><strong>Lock-free:</strong> SPSC ring buffers with volatile index semantics, no atomics or critical sections</li>
          <li><strong>Clocks:</strong> 8 MHz HSE to 84 MHz SYSCLK via PLL with correct flash latency ordering</li>
        </ul>
        <p class="tech">`C++17` `STM32F4` `Bare-metal` `CMSIS` `NVIC`</p>
        <p class="blog-link"><a href="/blog/bare-metal-uart-stm32/">→ read more</a></p>
      </div>
    </article>

    <!-- ServiceMesh -->
    <article class="project-card" data-category="software">
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
        <p class="blog-link"><a href="/blog/rust-servicemesh/">→ read more</a></p>
      </div>
    </article>

    <!-- Forge-DB -->
    <article class="project-card" data-category="software">
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
        <p class="blog-link"><a href="/blog/forge-db/">→ read more</a></p>
      </div>
    </article>

    <!-- ESP32-S3 RF Board -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/esp32-s3-rf-board-pcb.svg" alt="ESP32-S3 RF Board">
      <div class="card-content">
        <h3><a href="/hardware/esp32-s3-rf-board/">ESP32-S3 RF Board</a></h3>
        <p class="tagline">50x40mm 4-layer RF dev board, bare QFN56 die</p>
        <ul>
          <li><strong>RF:</strong> 50-ohm microstrip trace (0.6mm on L1), Johanson chip antenna + u.FL test port</li>
          <li><strong>Stackup:</strong> JLC04161H-7628, solid GND on L2, 3.3V plane on L3, 45-degree bends</li>
          <li><strong>Layout:</strong> GND stitching vias at 6mm (λ/10 at 2.4GHz), 5mm antenna keepout, parametric Python generation</li>
          <li><strong>Firmware:</strong> ESP-IDF v5 bring-up — dual-core WiFi scan + status LED validates RF path</li>
        </ul>
        <p class="tech">`KiCAD 8` `ESP32-S3` `RF Design` `4-Layer PCB` `ESP-IDF`</p>
        <p class="blog-link"><a href="/hardware/esp32-s3-rf-board/">→ view project</a></p>
      </div>
    </article>

    <!-- LoRa Sensor Node -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/lorawan-mesh-diagram.svg" alt="LoRa Sensor Node">
      <div class="card-content">
        <h3><a href="/hardware/lorawan-mesh/">LoRa Sensor Node</a></h3>
        <p class="tagline">Raw LoRa P2P sensor link with SPI-level debugging</p>
        <ul>
          <li><strong>Radio:</strong> SX1262 on 915MHz ISM, SF9/125kHz, raw LoRa modulation (not LoRaWAN)</li>
          <li><strong>Hardware:</strong> 2x Heltec WiFi LoRa 32 V3 (ESP32-S3 + SX1262 + OLED), DHT11 sensor</li>
          <li><strong>Debugging:</strong> 24MHz logic analyzer captures of SX1262 SPI bus with annotated command sequences</li>
          <li><strong>Protocol:</strong> Point-to-point TX/RX with RSSI/SNR monitoring, RadioLib driver</li>
        </ul>
        <p class="tech">`ESP32-S3` `SX1262` `LoRa` `915MHz` `PlatformIO` `RadioLib`</p>
        <p class="blog-link"><a href="/hardware/lorawan-mesh/">→ view project</a></p>
      </div>
    </article>

    <!-- Spectrum Analyzer -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/spectrum-analyzer-display.svg" alt="Spectrum Analyzer">
      <div class="card-content">
        <h3><a href="/hardware/spectrum-analyzer/">Spectrum Analyzer</a></h3>
        <p class="tagline">Portable RF spectrum analyzer with real-time OLED display</p>
        <ul>
          <li><strong>Display:</strong> Real-time spectrum visualization on OLED for RF debugging</li>
          <li><strong>Hardware:</strong> ESP32 + OLED + antenna pigtail + SD card logging</li>
          <li><strong>Use case:</strong> Debugging LoRa, WiFi, and BT projects in the field</li>
          <li><strong>Goal:</strong> Hands-on tool for learning radio frequency fundamentals</li>
        </ul>
        <p class="tech">`ESP32` `OLED` `RF` `Spectrum Analysis` `ESP-IDF`</p>
        <p class="blog-link"><a href="/hardware/spectrum-analyzer/">→ view project</a></p>
      </div>
    </article>

    <!-- STM32 Bare-Metal -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/stm32-dev-schematic.svg" alt="STM32 Bare-Metal">
      <div class="card-content">
        <h3><a href="https://github.com/HueCodes/UART-cpp">STM32 Bare-Metal</a></h3>
        <p class="tagline">ARM Cortex-M4 @ 84MHz, no HAL, no OS</p>
        <ul>
          <li><strong>No HAL:</strong> Direct register access via CMSIS only, multi-USART support (USART1/2/6)</li>
          <li><strong>Interrupt-driven:</strong> Non-blocking TX/RX with 256-byte SPSC ring buffers, volatile index semantics</li>
          <li><strong>Clocks:</strong> 8MHz HSE → PLL → 84MHz SYSCLK with correct flash wait-state ordering</li>
          <li><strong>Testing:</strong> 47 Catch2 unit tests on host via mock CMSIS headers, no hardware required</li>
        </ul>
        <p class="tech">`C++17` `STM32F401` `CMSIS` `Bare-metal` `Catch2`</p>
        <p class="blog-link"><a href="/hardware/stm32-dev/">→ view project</a></p>
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
        <p class="tech">`FreeRTOS` `STM32F411RE` `CMake` `BME280` `IWDG`</p>
        <p class="blog-link"><a href="/blog/freertos-stm32/">→ read more</a></p>
      </div>
    </article>

    <!-- Zephyr BLE Sensor -->
    <article class="project-card" data-category="hardware">
      <img src="/assets/images/projects/zephyr-ble-sensor.svg" alt="Zephyr BLE Sensor">
      <div class="card-content">
        <h3><a href="/hardware/zephyr-ble-sensor/">Zephyr BLE Sensor</a></h3>
        <p class="tagline">BLE environmental sensor on nRF52840 DK with GATT notifications</p>
        <ul>
          <li><strong>GATT:</strong> Standard Environmental Sensing Service (0x181A) + custom diagnostics service for BME280 readings</li>
          <li><strong>Zephyr:</strong> Device tree overlay for I2C sensor, Kconfig-driven BLE + PM subsystems, hardware watchdog (2s)</li>
          <li><strong>Power:</strong> Fast/slow advertising state machine (100ms→1000ms), connection interval negotiation, Zephyr PM sleep</li>
          <li><strong>Architecture:</strong> Sensor thread → k_msgq → system work queue → bt_gatt_notify, atomic diagnostic counters</li>
        </ul>
        <p class="tech">`Zephyr RTOS` `nRF52840` `BLE 5` `GATT` `Device Tree`</p>
        <p class="blog-link"><a href="/blog/zephyr-ble-sensor/">→ read more</a></p>
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
    <p class="oss-view-all">
      <a href="/opensource">View all contributions →</a>
    </p>
  </section>

  <footer>
    <p><a href="/blog">Writing</a> · <a href="/research">Research</a> · <a href="/hardware">Hardware</a></p>
  </footer>
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
