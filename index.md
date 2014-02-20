---
layout: default
---
{% include JB/setup %}

<hr>
Hi! I am Geoff Evans, software engineer at large (mostly gamedev, and usually tools).  I am known to do a bit of coding on [github](http://github.com/gorlak).  You can find personal ramblings at [flummoxed.org](http://flummoxed.org).
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
  {% if post.tagline %}
  <br><small>{{post.tagline}}</small>
  {% endif %}
  </h1>
  <div class="entry-content">{{ content }}</div>
</div>

<hr>
All posts:

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
