---
layout: page
title: ToyProject
permalink: /project/categories/ToyProject/
---

<h5> Posts by Category: {{ page.title }} </h5>

<div class="card">
{% for post in site.categories.ToyProject %}
 <li class="category-posts">
   <span>{{ post.date | date_to_string }}</span>
   &nbsp;
   <a href="{{ post.url }}">{{ post.title }}</a>
 </li>
{% endfor %}
</div>
