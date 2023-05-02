---
layout: page
title: Experience
permalink: experience
---

<div>
  {% for post in site.posts %}
    <div class="py-1">
      <h3><a href="{{site.baseurl}}{{ post.url }}">{{ post.title}}</a></h3>
      <div class="text-sm text-gray-400">{{post.start_date | date: "%B %Y"}} - {{post.end_date | date: "%B %Y"}}</div>
    </div>
  {% endfor %}
</div>


