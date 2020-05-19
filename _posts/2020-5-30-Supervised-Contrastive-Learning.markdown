---
layout: post
title:  "Supervised Contrastive Learning"
date:   2020-5-30
categories: 
tags: papers_analysis
---

<!-- <link href="https://fonts.googleapis.com/css?family=Prompt&display=swap" rel="stylesheet"> -->
<!-- <link rel="stylesheet" href="{{'/assets/css/style_font.css'}}" /> -->
<link rel="stylesheet" href="{{'/assets/css/style_categories.css'}}" />

{% if post %}
    {% assign tags = post.tags %}
{% else %}
    {% assign tags = page.tags %}
{% endif %}

{% if tags.size > 0 %}
<div class="post-tags catlinks">
  
  <a>Categories</a> :&nbsp;
  {% for tag in tags %}
  <a href="{{site.baseurl}}/tag/{{tag|slugize}}">{{tag}}</a>
  {% unless forloop.last %} &nbsp;| &nbsp;{% endunless %}
  {% endfor %}
</div>
{% endif %}

