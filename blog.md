---
layout: default
title: blog
---
<h1> blog </h1>
[subscribe on rss!](/feed.xml)
{% for post in site.posts %}
  <div>
    <h2 style="margin-bottom: 3px;"><a href="{{ post.url }}">{{ post.title | downcase }}</a></h2>
    <em>{{ post.date | date: "%Y-%m-%d" }} UTC</em>
    <p> {{ post.excerpt }}
{% endfor %}
