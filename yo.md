---
layout: page
title: "Yo"
description: ""
---
{% assign sorted_posts = (site.posts | sort: 'category') %}
{% for posts in sorted_posts%}
 {% if post.category == 'yo' %}
     * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})
    {% endif %}

{% endfor %}
