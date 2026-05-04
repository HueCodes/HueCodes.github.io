---
layout: default
title: open source
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="section">
  <h1>Open Source Contributions</h1>
</div>

<hr class="divider">

<div class="section">
  <div class="project">
    <a href="https://github.com/smoltcp-rs/smoltcp/pull/1127">smoltcp#1127</a>
    <span>Check silent_until expiry in Meta::poll_at. Fixes 100% CPU spin when a DNS query times out with an unresolved neighbor by falling through to socket_poll_at once the silence period expires.</span>
  </div>

  <div class="project">
    <a href="https://github.com/smoltcp-rs/smoltcp/pull/1126">smoltcp#1126</a>
    <span>TCP challenge ACK for duplicate ACKs in LAST-ACK state (RFC 9293 §3.10.7.4). Fixes silent discard of stale acknowledgments by sending a challenge ACK response.</span>
  </div>

  <div class="project">
    <a href="https://github.com/hyperium/hyper/pull/4011">hyper#4011</a>
    <span>Case-insensitive trailer field matching per RFC 9110. Fixes HTTP/2 trailer header handling to comply with spec requirements for case-insensitive field names.</span>
  </div>

  <div class="project">
    <a href="https://github.com/cilium/ebpf/pull/1945">cilium/ebpf#1945</a>
    <span>Add poller and eventRing interfaces for eBPF event handling. Extends the library's event processing capabilities for ring buffer operations.</span>
  </div>

  <div class="project">
    <a href="https://github.com/tokio-rs/tokio/pull/8086">tokio#8086</a>
    <span>Implement UCred::pid on FreeBSD via getsockopt(LOCAL_PEERCRED) reading xucred.cr_pid. Splits DragonFly into its own module since its xucred has no PID field. Closes a 5-year-old issue.</span>
  </div>

  <div class="project">
    <a href="https://github.com/embassy-rs/embassy/pull/5598">embassy#5598</a>
    <span>Align ring buffer reads to ADC scan sequence length. Fixes channel value shifting after DMA overrun recovery in STM32 ADC ring-buffered mode.</span>
  </div>

  <div class="project">
    <a href="https://github.com/mitmproxy/mitmproxy/pull/8046">mitmproxy#8046</a>
    <span>Fix modify_body crash when replacement contains backslash sequences. Resolves crash in mitmproxy's body modification addon caused by unescaped backslash sequences in regex substitution.</span>
  </div>

  <div class="project">
    <a href="https://github.com/SigmaHQ/pySigma/pull/442">pySigma#442</a>
    <span>Fix chained boolean operators in condition expressions. Extracts all operands from pyparsing results and folds them into a left-associative binary tree, fixing silent drops in expressions like A or B or C.</span>
  </div>

  <div class="project">
    <a href="https://github.com/SigmaHQ/sigma/pull/5899">SigmaHQ/sigma#5899</a>
    <span>Python base64 encoded inline command execution detection rules (Windows + Linux). High-confidence subset of existing python -c rule that matches base64.b64decode/b64encode calls to catch obfuscated reverse shells and droppers. T1059.006 + T1027.</span>
  </div>

  <div class="project">
    <a href="https://github.com/rust-lang/rust-clippy/pull/16402">rust-clippy#16402</a>
    <span>Fixed false positive lint for proc-macro generated code. Prevents incorrect warnings on procedurally generated Rust code that would otherwise pass compilation.</span>
  </div>

  <div class="project">
    <a href="https://github.com/dimforge/rapier/pull/806">rapier#806</a>
    <span>GeometricMean coefficient combine rule for friction simulation. Implements alternative friction model for more realistic physics simulation in the Rapier physics engine.</span>
  </div>

  <div class="project">
    <a href="https://github.com/cloudflare/agents/pull/781">cloudflare/agents#781</a>
    <span>Fix: properly type tool error content in getAITools. Corrects TypeScript type definitions for error handling in Cloudflare's AI agent framework.</span>
  </div>

  <div class="project">
    <a href="https://github.com/RustPython/RustPython/pull/6661">RustPython#6661</a>
    <span>Fixed set in-place operators with self argument. Resolves bug in Python set operations when operating on self (e.g., s |= s).</span>
  </div>

  <div class="project">
    <a href="https://github.com/lapce/floem/pull/1025">floem#1025</a>
    <span>Cache whitespace TextLayouts in editor paint. Performance optimization that caches text layout calculations for whitespace characters in the Floem UI framework.</span>
  </div>

  <div class="project">
    <a href="https://github.com/jeremyfix/torchcvnn/pull/106">torchcvnn#106</a>
    <span>Added input validation to normalization layers. Prevents runtime errors by validating tensor shapes and types in PyTorch complex-valued neural network library.</span>
  </div>
</div>
