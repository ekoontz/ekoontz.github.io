---
layout: page
title: Welcome to Eugene's Blog
tagbox: All Posts
---
{% include JB/setup %}

<p>I hope you find my blog posts interesting and informative.</p>

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>


