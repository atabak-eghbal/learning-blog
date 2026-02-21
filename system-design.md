---
layout: page
title: System Design
permalink: /system-design/
---

This section covers **System Design** — how to architect large-scale, reliable, and maintainable software systems. Posts explore distributed systems concepts, databases, caching, load balancing, message queues, API design, and trade-offs between consistency, availability, and partition tolerance.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "system-design" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
