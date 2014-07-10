---
layout: page
title: You Zhang's Blog
tagline: From SHU AMD ALU
---
{% include JB/setup %}

    
## My Blogs List


<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
