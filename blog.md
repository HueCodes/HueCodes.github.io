---
layout: default
title: blog
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
  <h1>blog</h1>
</div>

## Posts

{% assign regular_posts = site.posts | where_exp: "post", "post.category != 'projects'" %}
{% if regular_posts.size > 0 %}
<ul class="post-list">
  {% for post in regular_posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %Y" }}</time>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>nothing here yet.</p>
{% endif %}

---

## Projects

{% assign project_posts = site.posts | where: "category", "projects" %}
{% if project_posts.size > 0 %}
<ul class="post-list">
  {% for post in project_posts %}
  <li>
    <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
    <time datetime="{{ post.date | date_to_xmlschema }}">{{ post.date | date: "%b %Y" }}</time>
  </li>
  {% endfor %}
</ul>
{% else %}
<p>nothing here yet.</p>
{% endif %}
