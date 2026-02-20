---
layout: page
title: AI Engineering
permalink: /ai-engineering/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "ai-engineering" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
