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

1. A statement and
2. A test.

For example:

<div align="center">
The Statement | The Test
---|---
"The grass is green." | "It is *certain* that this is true."
"This code behaves correctly." | "It is *certain* that this is true."
"The release is ready." | "It is certain that this is true."
</div>

This can be accomplished by automated testing, or gating, in a DevOps pipeline.  The "**X**" intersection of the DevOps infinite loop is where this happens.

# The Gates to the Cloud

## Source Code Version Control
Use repositories for this.  Without this you can't track any issues found in any scans fruterh down the pipeline.  The 12 factor methodology outlines this too.

## Optimal Branching Strategy
The best branching strategy for commiting and managing code repositories will depend on your team's specific make-up and requirements.  See my post on [DevOps Branchign Strategies]({% post_url 2023-10-24-branching-strategies %}) for help with this.

## Unit Testing with Appropriate Percentage (%) Code Coverage
You might be wondering what an appropriate Unit Tes percentage coverage is.  Well the answer is - IT DEPENDS.  This will again depend on your team's specific make-up on team and/or developer maturity.  I found [this discussion on StackOverflow](https://stackoverflow.com/questions/90002/what-is-a-reasonable-code-coverage-for-unit-tests-and-why) nails this.  In simple terms, a random number percentage (%) does not equal good code coverage.  Tools such as SonarQube can help here.

## Automated Change Orders
This one is pretty simple - avoid manual committees and boards for approvals.  This creates waste and decreases efficiency, speed and does not create knowledge through testing.

## Vulnerability Testing
Here we introduce Dev**Sec**Ops.  DevSecOps integrates security practices seamlessly into the DevOps pipeline. It encompasses:

<div align="center">
DevSecOps Area | Description | Tooling
---|---|---
Dependency checks | examining and securing external software dependencies | National Vulnerability Database (NVD), Artifactory
Static Application Security Testing (SAST) | Identifying vulnerabilities in the source code | Checkmarx, Fortify, GitHub CodeQL
Dynamic Application Security Testing (DAST) | Assessing security during runtime | OWASP ZAP, Burp Suite, and Acunetix
</div>


