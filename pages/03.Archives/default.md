---
title: Archives
process:
    markdown: true
    twig: true
twig_first: true
content:
    items: '''@page.children'': /blog'
    leading: '0'
    columns: '3'
    limit: '5'
    order:
        by: date
        dir: asc
    show_date: '1'
    pagination: '1'
    url_taxonomy_filters: '1'
---

##归档按页面

<ul>
{% set this_year = "now"|date('Y') %}
{% for post in page.find('/blog').children.dateRange('01/01/' ~ this_year, '12/31/' ~ this_year).order('date', 'desc') %}
    <li class="recent-posts">
        <strong><a href="{{ post.url }}">{{ post.title }}</a></strong>
    </li>
{% endfor %}
</ul>
