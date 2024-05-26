---
layout: page
title: About
permalink: /about/
---

Welcome to TreasureBoat and it's members.

<ul>
{% for member in site.data.members %}
  <li>
    <a href="https://github.com/{{ member.github }}">
      {{ member.name }}
    </a>
  </li>
{% endfor %}
</ul>
