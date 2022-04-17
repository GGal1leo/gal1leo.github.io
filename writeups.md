---
layout: page
title: Writeups
---

<section>

  <h2 style="font-family: 'ohgodno';font-size: 300%;">./WRITEUPS</h2>
  {% for tag in site.tags %}
  <h2 style="font-family: 'ohgodno'; color:#169403;">./{{ tag[0] }}</h2>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
  {% endfor %}
<!-- <h4>site tags are : {{ site.tags }}</h4> -->
</section>
