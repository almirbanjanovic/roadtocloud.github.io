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

{% include feature_row id="intro" type="left" %}

{% if page.url != "/" and site.breadcrumbs %}
  {% unless paginator %}
    {% include breadcrumbs.html %}
  {% endunless %}
{% endif %}

<div id="main" role="main">
  {% include sidebar.html %}

  <div class="archive">
    {% unless page.header.overlay_color or page.header.overlay_image %}
      <h1 id="page-title" class="page__title">{{ page.title }}</h1>
    {% endunless %}
    {{ content }}
  </div>
</div>

# Latest
{% for post in site.posts %}
  [{{ post.title }}]({{ post.url }})
{% endfor %}
