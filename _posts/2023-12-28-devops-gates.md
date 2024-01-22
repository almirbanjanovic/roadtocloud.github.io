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

The Statement | The Test
---|---
"The grass is green." | "It is *certain* that this is true."
"This code behaves correctly." | "It is *certain* that this is true."
"The release is ready." | "It is certain that this is true."

This can be accomplished by automated testing, or gating, in a DevOps pipeline.  The "**X**" intersection of the DevOps infinite loop is where this happens.

# The Gates to the Cloud

## Source Code Version Control
Use repositories for this.  Without this you can't track any issues found in any scans fruterh down the pipeline.  The 12 factor methodology outlines this too.  We can use [Azure DevOps Repos](https://azure.microsoft.com/en-us/products/devops/repos/) and [GitHub](https://github.com) for this purpose.

## Artifact Version Control
It is very important to systematically manage and track changes to binary artifacts, such as open source packages, Docker images, VM images and build pipeline artifacts. A very good tool for something like this is [Artifactory](https://jfrog.com/artifactory/).  It plays a crucial role in DevOps gating by providing a centralized and secure repository for binary artifacts.  

## Optimal Branching Strategy
The best branching strategy for commiting and managing code repositories will depend on your team's specific make-up and requirements.  See my post on [DevOps Branching Strategies]({% post_url 2023-10-24-branching-strategies %}) for help with this.

## Unit Testing
Unit testing is essential to shifting testing left so issues are identified before they're even deployed.  You might be wondering what an appropriate Unit Tes percentage coverage is.  Well the answer is - IT DEPENDS.  This will again depend on your team's specific make-up on team and/or developer maturity.  I found [this discussion on StackOverflow](https://stackoverflow.com/questions/90002/what-is-a-reasonable-code-coverage-for-unit-tests-and-why) nails this.  In simple terms, a random number percentage (%) does not equal good code coverage.  Tools such as SonarQube can help here.

## Integration Testing
Integration testing ensures that all components work together as intended in any environment, which should be carbon copies of one another for predictability, stability and reliability.  For example, in a web application scenario, integration testing ensures that user interfaces interact correctly with a RESTful API, which, in turn, communicates seamlessly with the underlying database. Testing might involve validating data flow, error handling, and overall system behavior to guarantee cohesion and reliability across the entire stack.

## Performance and Load Testing
Here we need to make sure to test throughput of an application.  This ivolves testing CPU load, memory (RAM) load, response times and service level agreements (SLAs) are met.  For example, evaluating how the web application performs under a specified number of concurrent users, how the REST API responds to increased requests, and how the database manages a higher volume of transactions. This testing ensures that the different components can sustain optimal performance and reliability under varying load conditions, helping identify and address potential bottlenecks. Here we can use tools such as Apache [JMeter}(https://jmeter.apache.org/), [Loadrunner](https://www.microfocus.com/en-us/portfolio/performance-engineering/overview) and [Grafana k6](https://k6.io/).

## Vulnerability Testing
Here we introduce Dev**Sec**Ops.  DevSecOps integrates security practices seamlessly into the DevOps pipeline. It encompasses:

DevSecOps Area | Description | Tooling
---|---|---
Dependency checks / Open Source Scanning | Examining and securing external software dependencies, such as code from third party sources | [Artifactory](https://jfrog.com/artifactory/), [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/), [National Vulnerability Database (NVD)](https://nvd.nist.gov/)
Static Application Security Testing (SAST) | Identifying vulnerabilities in the source code | [Checkmarx](https://checkmarx.com/), [Fortify](https://www.microfocus.com/en-us/cyberres/application-security/static-code-analyzer), [GitHub CodeQL](https://codeql.github.com/)
Dynamic Application Security Testing (DAST) | Assessing security during runtime | [OWASP ZAP](https://www.zaproxy.org/), [Burp Suite](https://portswigger.net/burp), and [Acunetix](https://www.acunetix.com/)

## Automated Change Orders
This one is pretty simple - avoid manual committees and boards for approvals.  This creates waste and decreases efficiency, speed and does not create knowledge through testing.
