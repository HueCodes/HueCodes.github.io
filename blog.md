---
layout: default
title: blog
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="/opensource">opensource</a>
  <a href="/hardware">hardware</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

<div class="intro">
  <h1>blog</h1>
</div>

## Posts

{% if site.posts.size > 0 %}
<ul class="post-list">
  {% for post in site.posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %Y" }}</time>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>nothing here yet.</p>
{% endif %}

## Research

{% if site.research.size > 0 %}
<ul class="post-list">
  {% for item in site.research %}
  <li>
    <a href="{{ item.url | relative_url }}">{{ item.title }}</a>
    <time>{{ item.date | date: "%b %Y" }}</time>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>nothing here yet.</p>
{% endif %}
