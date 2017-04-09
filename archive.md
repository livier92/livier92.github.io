---
layout: page
title: Archive
sitemap: false
---

<div>
{% assign categories = site.categories | sort %}
{% for category in categories %}
  <h2><b>{{category[0]}}</b></h2>

  {% for post in site.posts %}
  {%if post.categories contains category[0]%}
  * {{ post.date | date_to_string }} &raquo; [ {{ post.title }} ]({{ post.url }})

  {%endif%}
  {% endfor %}

{% endfor %}
</div>
