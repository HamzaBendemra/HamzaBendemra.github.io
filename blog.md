---
layout: page
title: Blog
permalink: /blog/
---

<div class="home">
  <ul class="post-list">
    {% for post in site.posts %}
      <li class="post-card">
        <span class="post-meta">{{ post.date | date: "%b %-d, %Y" }}</span>
        <h3><a class="post-link" href="{{ post.url | relative_url }}">{{ post.title | escape }}</a></h3>
        {% if post.description %}
          <p class="post-excerpt">{{ post.description }}</p>
        {% endif %}
        <div class="post-tags">
          {% for tag in post.tags limit:3 %}
            <span class="tag">{{ tag }}</span>
          {% endfor %}
        </div>
      </li>
    {% endfor %}
  </ul>
</div>
