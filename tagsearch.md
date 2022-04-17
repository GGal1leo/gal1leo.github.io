---
layout: page
title: JOE
---

{% comment %}Posts will be filtered by one category{% endcomment %}
{% assign filterCategory = "writeups" %}

{% for tag in site.tags %}
    {% comment %}creates an empty array{% endcomment %}
    {% assign postsInCategory = "" | split: "/" %}

    {% comment %}looping over site.tags{% endcomment %}
    {% for post in tag[1] %}
        {% if post.categories contains filterCategory %}
            {% comment %}if a post is from our filter category we add it to postsInCategory array{% endcomment %}
            {% assign postsInCategory = postsInCategory | push: post %}
        {% endif %}
    {% endfor %}

    {% if postsInCategory.size > 0 %}
<h2 style="font-family: 'ohgodno';font-size: 300%;">{{ tag[0] }}</h2>
        {% for post in postsInCategory %}
            <h3><a href="{{ post.url }}">{{ post.title }}</a></h3>
        {% endfor %}
    {% endif %}
{% endfor %}