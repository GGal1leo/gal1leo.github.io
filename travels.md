---
layout: page
title: Writeups
---

<section>

  <h2 style="font-family: 'ohgodno';font-size: 300%;">./Travels</h2>
  <ul>
    {% for post in site.categories.travels %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>

<!-- <h4>site tags are : {{ site.tags }}</h4> -->
</section>