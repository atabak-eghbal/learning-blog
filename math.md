---
layout: page
title: Math
permalink: /math/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "math" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
