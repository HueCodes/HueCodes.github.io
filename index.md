---
layout: default
title: home
---

<nav>
  <a href="/">home</a>
  <a href="/projects">projects</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="/research">research</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro fade-in">
  <div class="intro-header">
    <img src="/assets/DSCN1683.jpeg" alt="Hugh's profile photo" class="photo">
    <div>
      <h1>hey, i'm hugh</h1>
      <p>systems and networking engineer in seattle. i build infrastructure software, embedded firmware, and hardware.</p>
      <p>outside of engineering i like being in the mountains, grappling, and running ultra marathons.</p>
      <p class="tagline">i ship fast and want to solve hard problems with smart people.</p>
      <p><a href="mailto:huecodes@proton.me">huecodes@proton.me</a></p>
    </div>
  </div>
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.1s">
  <h2>currently</h2>
  <p>contributing to <a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf</a> and <a href="https://github.com/tektoncd/pipeline/pull/9590">tektoncd/pipeline</a>. building tunnel management and udp relay infrastructure in go. studying wireguard internals and nat traversal.</p>
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.15s">
  <h2>selected work</h2>
  <ul class="work-list">
    <li><a href="https://github.com/HueCodes/gretun"><strong>gretun</strong></a> <span class="lang">go</span> — gre tunnel management cli via netlink for multi-cloud vpc interconnection</li>
    <li><a href="https://github.com/HueCodes/Go-Hunter"><strong>go-hunter</strong></a> <span class="lang">go</span> — multi-cloud asset discovery platform, 1k assets/min across aws/gcp/azure</li>
    <li><a href="https://github.com/HueCodes/udp-relay"><strong>udp-relay</strong></a> <span class="lang">go</span> — high-throughput udp ingestion server with zero-copy parsing and websocket fan-out</li>
    <li><a href="https://github.com/HueCodes/Rust-ServiceMesh"><strong>servicemesh</strong></a> <span class="lang">rust</span> — l7 data plane proxy, 52m ops/sec circuit breaker, http/2 and grpc</li>
    <li><a href="https://github.com/HueCodes/Sensor-Bridge"><strong>sensor-bridge</strong></a> <span class="lang">rust</span> — lock-free sensor fusion pipeline, ~20ns latency, no_std compatible</li>
    <li><a href="https://github.com/HueCodes/forge-db"><strong>forge-db</strong></a> <span class="lang">rust</span> — simd-accelerated vector database, 13k qps with avx2/avx-512</li>
  </ul>
  <p class="view-all"><a href="/projects">all projects →</a></p>
</div>

<hr class="divider">

<div class="section fade-in" style="animation-delay: 0.2s">
  <h2>open source</h2>
  <ul class="work-list">
    <li><a href="https://github.com/cilium/ebpf/pull/1945"><strong>cilium/ebpf</strong></a> <span class="lang">go</span> — cross-platform ringbuf interfaces for windows poller support</li>
    <li><a href="https://github.com/grpc/grpc-go"><strong>grpc-go</strong></a> <span class="lang">go</span> — target uri validation</li>
    <li><a href="https://github.com/tektoncd/pipeline/pull/9590"><strong>tektoncd/pipeline</strong></a> <span class="lang">go</span> — strip managedfields from informer caches, 30-70% size reduction</li>
    <li><a href="https://github.com/tokio-rs/tokio/pull/7874"><strong>tokio</strong></a> <span class="lang">rust</span> — lock-free cancellation via atomicbool, fixing >2ms mutex delays</li>
    <li><a href="https://github.com/hyperium/hyper/pull/4011"><strong>hyper</strong></a> <span class="lang">rust</span> — rfc 9110 trailer field matching</li>
    <li><a href="https://github.com/smoltcp-rs/smoltcp"><strong>smoltcp</strong></a> <span class="lang">rust</span> — tcp challenge ack, dns timeout fix</li>
  </ul>
  <p class="view-all"><a href="/opensource">all contributions →</a></p>
</div>

<hr class="divider">

<footer>
  <p><a href="/blog">writing</a> · <a href="/research">research</a> · <a href="/hardware">hardware</a></p>
</footer>
