---
layout: page
title: Main Page
---
{% include JB/setup %}

## Welcome Stranger

I am Krzysztof Kaczor and I work as software developer. This place is intended to be a shelter for my thoughts about computer science and software development in general.

## Posts

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
