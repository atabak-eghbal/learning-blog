---
layout: page
title: ML Fundamentals
permalink: /ml-fundamentals/
---

This section covers **Machine Learning Fundamentals** — the core concepts every ML practitioner needs to understand deeply. Posts explore supervised and unsupervised learning, loss functions, optimization, regularization, evaluation metrics, and the mathematical intuition behind classical and modern ML models.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "ml-fundamentals" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
