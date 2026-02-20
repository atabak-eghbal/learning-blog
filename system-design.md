---
layout: page
title: System Design
permalink: /system-design/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "system-design" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
