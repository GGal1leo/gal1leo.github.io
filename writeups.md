---
layout: page
title: Archive
---

<section>
{% for tag in site.tags %}
  <h2>{{ "writeups" }}</h2>
  <ul>
    {% for post in tag[1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
{% endfor %}
<h4>site tags are : {{ site.tags }}</h4>
</section>
