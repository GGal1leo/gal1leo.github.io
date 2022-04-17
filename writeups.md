---
layout: page
title: Archive
---

<section>
<!-- {% for tag in site.tags %} -->
  <h3>{{ "writeups" }}</h3>
  <ul>
    {% for post in "writeups" %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
<!-- {% endfor %} -->
</section>
