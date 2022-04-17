---
layout: page
title: Writeups
k: []
---
{% for post in site.categories.writeups %}
  {% assign tweetsAndPosts = post.tags | concat: k %}
{% endfor %}

k[0]

<section>

  <h2 style="font-family: 'ohgodno';font-size: 300%;">./WRITEUPS</h2>

  <ul>
    {% for post in site.categories.writeups %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>

<!-- <h4>site tags are : {{ site.tags }}</h4> -->
</section>
