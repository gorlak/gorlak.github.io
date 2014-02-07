---
layout: default
---
{% include JB/setup %}

<hr>
Hi! I am Geoff Evans, programmer at large (mostly gamedev, and usually gamedev tools).  You can find me on [twitter](http://twitter.com/gorlak) and [github](http://github.com/gorlak).  Until then, feel free and read my latest post:
<hr>

<div class="blog-index">  
  {% assign post = site.posts.first %}
  {% assign content = post.content %}
  <h1 class="entry-title">
  {% if page.title %}
      <a href="{{ root_url }}{{ page.url }}">{{ page.title }}</a>
  {% endif %}
  {% if post.title %}
      <a href="{{ root_url }}{{ post.url }}">{{ post.title }}</a>
  {% endif %}
  </h1>
  <div class="entry-content">{{ content }}</div>
</div>

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
