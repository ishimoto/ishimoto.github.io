---
published: true
layout: page
title: Categories
permalink: /category
---

Here you find a list about all the categorized items.

{% for category in site.categories %}
  <h3>{{ category[0] }}</h3>
  <ul>
    {% for post in category[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
