---
layout: page
title: Archive
---

<section>
<!-- {% for tag in site.tags %} -->
  <h2>WRITEUPS!</h2>
  <ul>
    {% for post in site.tags[0][1] %}
      <li><a href="{{ post.url }}">{{ post.title }}</a></li>
    {% endfor %}
  </ul>
<!-- {% endfor %} -->
<h4>site tags are : {{ site.tags }}</h4>
</section>
