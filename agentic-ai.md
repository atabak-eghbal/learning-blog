---
layout: page
title: Agentic AI
permalink: /agentic-ai/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "agentic-ai" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
