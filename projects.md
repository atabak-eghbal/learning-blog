---
layout: page
title: Projects
permalink: /projects/
---

This section showcases **Projects** — hands-on work and experiments that apply the concepts explored throughout this blog. Each post documents what was built, the key technical decisions made, lessons learned, and pointers to the code or demo where available.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "projects" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
