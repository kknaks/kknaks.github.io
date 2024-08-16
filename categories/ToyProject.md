---
layout: default
title: ToyProject
permalink: /project/categories/ToyProject/
---

<h5> Projects by Category: {{ page.title }} </h5>

<div class="card">
  <ul>
    {% for project in site.projects %}
      {% if project.category contains "ToyProject" %}
        <li class="category-posts">
          <span>{{ project.date | date_to_string }}</span>
          &nbsp;
          <a href="{{ project.url | prepend: site.baseurl }}">{{ project.title }}</a>
        </li>
      {% endif %}
    {% endfor %}
  </ul>
</div>
