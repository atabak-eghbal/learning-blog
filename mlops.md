---
layout: page
title: MLOps
permalink: /mlops/
---

This section covers **MLOps** — the practices and tools for deploying, monitoring, and maintaining machine learning systems in production. Topics include CI/CD for ML, experiment tracking, model serving, data pipelines, and infrastructure-as-code patterns for scalable ML workflows.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "mlops" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
