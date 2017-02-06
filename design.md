---
layout: page
title: Design
---
<ul>
{% for post in site.categories.architecture %}
   <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

