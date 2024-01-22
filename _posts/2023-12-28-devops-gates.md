---
title:  "DevOps Gates"
header:
  teaser: "/assets/images/devops-gating.jpg"
excerpt: "The Gates to the Cloud - delivery pipeline checkpoints for healthy, resilient, and secure applications."
toc: true
tags:
  - devops
  - sdlc
  - strategy
---
<figure style="width: 300px" class="align-left">
  <img src="{{ site.url }}{{ site.baseurl }}/assets/images/devops-gating.jpg" alt="">
</figure> 
<br>
DevOps gating is a practice in software development that involves setting up checkpoints or gates to make sure the application code is ready for deployment. One of the core messages of [Keep it Lean and Eliminate Waste]({% post_url 2023-12-20-keep-it-lean %}) is the concept of "epistemic humility" - that we need to accept that quality of our knowledge about a specific application component in delivery is poor and we need to plan with that in mind. As a result, two separate propositions that have to be evaluated independently:

1. A statement
2. A test

For example:

<div align="center">
The Statement | The Test
---|---
"The grass is green." | "It is *certain* that this is true."
"This code behaves correctly." | "It is *certain* that this is true."
"The release is ready." | "It is certain that this is true."
</div>

This can be accomplished by automated testing, or gating, in a DevOps pipeline.  The "**X**" intersection of the DevOps infinite loop is where this happens.

