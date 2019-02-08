---
layout: default
menutitle: Categories
title: Tous les articles
---

{% for category in site.categories %}
  <h3>{{ category[0] }}</h3>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
{% endfor %}
