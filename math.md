---
layout: page
title: Math
permalink: /math/
---

This section covers the **Mathematics** underpinning machine learning and computer science. Topics include linear algebra, calculus, probability theory, statistics, and discrete math — revisited with an emphasis on the intuitions that matter most for practical ML and algorithmic work.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "math" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
