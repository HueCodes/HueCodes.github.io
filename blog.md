---
layout: default
title: blog
---

<nav>
  <a href="/">home</a>
  <a href="/blog">blog</a>
  <a href="https://github.com/HueCodes">github</a>
</nav>

# blog

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
nothing here yet. check back soon.
{% endif %}
