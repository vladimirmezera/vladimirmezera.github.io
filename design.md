---
layout: page
title: IT
priority: 2 
---
<ul>
{% for post in site.categories.architecture %}
   <li><a href="{{ post.url }}">{{ post.title }}</a></li>
{% endfor %}
</ul>

