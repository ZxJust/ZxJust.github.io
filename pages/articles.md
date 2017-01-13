---
title: 目录
layout: default
permalink: /articles/
favicon: /assets/img/fav/articles.png
---

{% for category in site.categories %}
  <div class="category-item">
      <div class="category-title">{{ category | first | capitalize }}</div>
      <div class="category-count">{{ category | last | size }}</div>
  </div>
  {% for post in category.last %}
  <div class="article-item">
      <div class="article-title">
          <a href="{{ post.url }}" target="_blank" >{{ post.title }}</a>
          <span class="article-date">{{post.date | date:"%Y %m-%d"}}</span>
      </div>
  </div>
  {% endfor %}
{% endfor %}
