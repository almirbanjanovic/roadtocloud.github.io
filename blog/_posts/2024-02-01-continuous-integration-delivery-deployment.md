---
title:  "What exactly does CI/CD mean?"
header:
  teaser: "/assets/images/continuous-integration-delivery-deployment.png"
excerpt: "I often see people confusing the terms *Continuous Integration, Continuous Delivery and Continuous Deployment*.  Let's break it down in simpler terms."
tags:
  - cloud
  - code
  - devops
---


I often see people confusing the terms *Continuous Integration, Continuous Delivery and Continuous Deployment*.  Let's break it down in simpler terms.

<figure>
    <a href="/assets/images/continuous-integration-delivery-deployment.png"><img src="/assets/images/continuous-integration-delivery-deployment.png"></a>
</figure>

Continuous Integration (CI) involves developers merging their tested code into a common branch.  Depending on the branching strategy used, this can be as often as multiple times a day, or a few times a week.  The next phase is Continuous Delivery (CD).  Here, we need to make sure to package the code in an artifact and store it somewhere - this is our release artifact.  Finally, there is Continuous Deployment (CD).  This takes Continuous Delivery (CD) a step further by sending our finished release artifact out into the real world for people to use. It's all about making things smoother and faster! 
