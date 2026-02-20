---
layout: page
title: MLOps
permalink: /mlops/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "mlops" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
