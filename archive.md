---
layout: page
title: Archive
permalink: /archive/
---

{% assign postsByYear = site.posts | group_by_exp: "post", "post.date | date: '%Y'" %}

{% for year in postsByYear %}
  <h2 id="{{ year.name }}">{{ year.name }}</h2>
  <ul>
    {% for post in year.items %}
      <li>
        <span class="post-date">{{ post.date | date: "%b %d" }}</span> &raquo; 
        <a href="{{ post.url | relative_url }}">{{ post.title }}</a>
      </li>
    {% endfor %}
  </ul>
{% endfor %}