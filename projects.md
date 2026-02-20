---
layout: page
title: Projects
permalink: /projects/
---

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "projects" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
