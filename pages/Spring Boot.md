---
layout: page
title: "Spring Boot"
description: ""
---
<ul class="posts">
{% for post in site.posts %}
    {% if post.categories contains 'Spring Boot' %}
        <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
    {% endif %}
{% endfor %}
</ul>
