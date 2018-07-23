---
title: CV
layout: default
---

{% for item in site.experience %}
  <h2><a href="{{ item.url }}">{{ item.title }}</a></h2>
{% endfor %}
