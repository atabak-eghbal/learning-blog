---
layout: page
title: Agentic AI
permalink: /agentic-ai/
---

This section covers **Agentic AI** — systems where AI models take sequences of actions, use tools, and reason toward goals with varying degrees of autonomy. Posts explore agent architectures, planning strategies, tool use, multi-agent coordination, and the emerging frameworks that make building reliable AI agents possible.

<ul>
  {% for post in site.posts %}
    {% if post.categories contains "agentic-ai" %}
      <li><a href="{{ post.url | relative_url }}">{{ post.title }}</a> — {{ post.date | date: "%Y-%m-%d" }}</li>
    {% endif %}
  {% endfor %}
</ul>
