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

<table>
    <tr>
        <td id="left">Left</td>
        <td id="center">Center</td>
        <td id="right">Right</td>
    </tr>
    <tr>
        <td>{% assign random = site.time | date: "%s%N" | modulo: site.posts.size %} {{ site.posts[random].title }}</td>
        <td>{% assign random = site.time | date: "%s%N" | modulo: site.posts.size %} {{ site.posts[random].title }}</td>
        <td>{% assign random = site.time | date: "%s%N" | modulo: site.posts.size %} {{ site.posts[random].title }}</td>
    </tr>
</table>

