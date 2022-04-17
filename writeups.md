---
layout: page
title: Archive
---

<section>
{% for tag in site.tags %}
  <h3>{{ "writeups" }}</h3>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a>
      DEBUG: {{ tag[0] }} and {{ tag[1] }}</li>
    {% endfor %}
  </ul>
{% endfor %}
</section>
