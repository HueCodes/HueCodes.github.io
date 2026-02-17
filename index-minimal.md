---
layout: default
title: home
---

<div class="minimal-layout">
  <!-- Simple Header -->
  <header class="minimal-header">
    <h1>Hugh</h1>
    <p>Go · Cloud Infrastructure · Distributed Systems</p>
    <nav class="minimal-nav">
      <a href="/blog">writing</a>
      <a href="/research">research</a>
      <a href="/hardware">hardware</a>
      <a href="https://github.com/HueCodes">github</a>
      <a href="mailto:huecodes@proton.me">email</a>
    </nav>
  </header>

  <!-- Projects Grid with Images -->
  <section class="projects-minimal">
    <div class="project-item">
      <a href="https://github.com/HueCodes/Go-Hunter" class="project-link">
        <img src="/assets/images/projects/go-hunter-dashboard.png" alt="Go-Hunter Dashboard" class="project-image">
        <div class="project-info">
          <h2>Go-Hunter</h2>
          <p>Multi-cloud attack surface management · 1K assets/min</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/forge-db" class="project-link">
        <img src="/assets/images/projects/forge-db-architecture.png" alt="Forge-DB Architecture" class="project-image">
        <div class="project-info">
          <h2>Forge-DB</h2>
          <p>SIMD vector database · 13K QPS at 75µs</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/Rust-ServiceMesh" class="project-link">
        <img src="/assets/images/projects/servicemesh-architecture.png" alt="ServiceMesh Architecture" class="project-image">
        <div class="project-info">
          <h2>ServiceMesh</h2>
          <p>L7 proxy with circuit breakers · 52M ops/sec</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/nodix" class="project-link">
        <img src="/assets/images/projects/nodix-sensor-fusion.png" alt="Nodix Sensor Fusion" class="project-image">
        <div class="project-info">
          <h2>Nodix</h2>
          <p>Real-time compute graph · <1ms p99 latency</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/gretun" class="project-link">
        <img src="/assets/images/projects/gretun-tunnel.png" alt="Gretun Tunnel" class="project-image">
        <div class="project-info">
          <h2>Gretun</h2>
          <p>GRE tunnel management · Cloud VPC interconnect</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/Sensor-Bridge" class="project-link">
        <img src="/assets/images/projects/sensor-bridge-pipeline.png" alt="Sensor Bridge Pipeline" class="project-image">
        <div class="project-info">
          <h2>Sensor-Bridge</h2>
          <p>Lock-free sensor fusion · 2.2B items/sec</p>
        </div>
      </a>
    </div>

    <div class="project-item">
      <a href="https://github.com/HueCodes/Compression-Algo-Huffman" class="project-link">
        <img src="/assets/images/projects/huffman-tree.svg" alt="Huffman Tree" class="project-image">
        <div class="project-info">
          <h2>Huffman-Cpp</h2>
          <p>Modern C++17 compression · Smart pointers, RAII</p>
        </div>
      </a>
    </div>
  </section>

  <!-- Footer -->
  <footer class="minimal-footer">
    <p>Seattle · <a href="https://github.com/HueCodes">GitHub</a> · <a href="/blog">Blog</a></p>
  </footer>
</div>

<style>
.minimal-layout {
  max-width: 1200px;
  margin: 0 auto;
  padding: 60px 20px;
}

.minimal-header {
  text-align: center;
  margin-bottom: 80px;
}

.minimal-header h1 {
  font-size: 48px;
  margin: 0 0 10px 0;
  font-weight: 600;
}

.minimal-header > p {
  color: #666;
  margin: 0 0 30px 0;
  font-size: 16px;
}

.minimal-nav {
  display: flex;
  gap: 24px;
  justify-content: center;
  margin-top: 20px;
}

.minimal-nav a {
  color: #666;
  text-decoration: none;
  font-size: 14px;
  transition: color 0.2s;
}

.minimal-nav a:hover {
  color: #000;
}

.projects-minimal {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(350px, 1fr));
  gap: 40px;
  margin-bottom: 80px;
}

.project-item {
  position: relative;
  overflow: hidden;
  border-radius: 8px;
  background: #fafafa;
  transition: transform 0.2s, box-shadow 0.2s;
}

.project-item:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 24px rgba(0,0,0,0.1);
}

.project-link {
  display: block;
  text-decoration: none;
  color: inherit;
}

.project-image {
  width: 100%;
  height: 240px;
  object-fit: cover;
  display: block;
  border-bottom: 1px solid #eee;
}

.project-info {
  padding: 20px;
}

.project-info h2 {
  margin: 0 0 8px 0;
  font-size: 20px;
  font-weight: 600;
}

.project-info p {
  margin: 0;
  color: #666;
  font-size: 14px;
  line-height: 1.5;
}

.minimal-footer {
  text-align: center;
  padding-top: 40px;
  border-top: 1px solid #eee;
  color: #999;
  font-size: 14px;
}

.minimal-footer a {
  color: #666;
  text-decoration: none;
}

.minimal-footer a:hover {
  color: #000;
}

@media (max-width: 768px) {
  .projects-minimal {
    grid-template-columns: 1fr;
    gap: 30px;
  }

  .minimal-header h1 {
    font-size: 36px;
  }
}
</style>
