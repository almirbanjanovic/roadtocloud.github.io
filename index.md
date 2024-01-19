---
title: "Road to Cloud"
layout: splash
header:
  overlay_color: "#000"
  overlay_filter: "0.5"
  overlay_image: /assets/images/clouds.jpg
excerpt: "Chronicles of grand strategy to imaginatively crafted solutions with creative implementations!"
intro: 
  - excerpt: "Welcome to Road to Cloud, your guide to crafting high-performance, cost-effective, and resilient solutions for your enterprise journey."
---

{% include feature_row id="intro" type="center" %}

# Latest
{% for post in site.posts limit:1 %}
  [{{ post.title }}]({{ post.url }})
  {{ post.excerpt }}
{% endfor %}

## Recent
{% for post in site.posts offset:1 limit:3 %}
  [{{ post.title }}]({{ post.url }})
  {{ post.excerpt }}
{% endfor %}
