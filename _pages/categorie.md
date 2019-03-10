---
ayout: page
author: agailloty
menutitle: Site
title: Tous les articles
---
<body>
{% for category in site.categories %}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url/blog }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
  </body>