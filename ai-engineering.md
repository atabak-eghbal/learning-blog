---
layout: page
title: AI Engineering
permalink: /ai-engineering/
---

This section covers **AI Engineering** — the craft of building production-grade AI systems. Topics include prompt engineering, retrieval-augmented generation (RAG), fine-tuning, LLM evaluation, vector databases, and the architectural patterns for integrating large language models into real-world applications.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "ai-engineering" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
