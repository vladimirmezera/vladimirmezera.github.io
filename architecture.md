---
layout: page
title: Architecture
---

    {% for posts in site.categories.architecture %}
      {% for post in posts %}
        <li><a href="{{ post.url }}">{{ post.title }}</a></li>
      {% endfor %}
    {% endfor %}


