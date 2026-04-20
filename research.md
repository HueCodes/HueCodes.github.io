---
layout: default
title: research
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>research</h1>
</div>

<div class="section">
  {% for item in site.research %}
  <div class="project">
    <a href="{{ item.url | relative_url }}">{{ item.title }}</a>
    {% if item.summary %}<span>{{ item.summary }}</span>{% endif %}
  </div>
  {% endfor %}
</div>
