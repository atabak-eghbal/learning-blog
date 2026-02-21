---
layout: page
title: DSA
permalink: /dsa/
---

This section covers **Data Structures & Algorithms** — the building blocks of efficient software. Posts explore fundamental patterns such as sliding window, two pointers, binary search, hash maps, trees, graphs, and dynamic programming. Each entry breaks down the concept, walks through examples, and highlights common pitfalls to watch out for.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "dsa" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
