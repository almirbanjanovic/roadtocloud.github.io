---
title:  "DevOps Code Quality Gates"
header:
  teaser: "/assets/images/devops-gating.jpg"
excerpt: "The Gates to the Cloud - delivery pipeline checkpoints for healthy, resilient, and secure applications."
toc: true
tags:
  - devops
  - code
  - sdlc
  - strategy
---

<figure>
	<a href="/assets/images/devops-gating.jpg"><img src="/assets/images/devops-gating.jpg"></a>
	<figcaption>The Gates to the Cloud</figcaption>
</figure>

DevOps code quality gating is a practice in software development that involves setting up checkpoints or gates to make sure the application code is ready for deployment. One of the core messages of [Keep it Lean and Eliminate Waste]({% post_url 2023-12-20-keep-it-lean %}) is the concept of "epistemic humility" - that we need to accept that quality of our knowledge about a specific application component in delivery is poor and we need to plan with that in mind. As a result, there are two separate propositions that have to be evaluated independently:

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
Use repositories for this.  Without this you can't track any issues found in any scans further down the pipeline.  The [12 factor methodology](https://12factor.net) outlines this too.  We can use [Azure DevOps Repos](https://azure.microsoft.com/en-us/products/devops/repos/) and [GitHub](https://github.com) for this purpose.

## Artifact Version Control
It is very important to systematically manage and track changes to binary artifacts, such as open source packages, Docker images, VM images and build pipeline artifacts. A very good tool for something like this is [Artifactory](https://jfrog.com/artifactory/).  It plays a crucial role in DevOps quality gating by providing a centralized and secure repository for binary artifacts.  

## Optimal Branching Strategy
The best branching strategy for commiting and managing code repositories will depend on your team's specific make-up and requirements.  See my post on [DevOps Branching Strategies]({% post_url 2023-10-24-branching-strategies %}) for help with this.

## Unit Testing
Unit testing is essential to shifting testing left so issues are identified before they're even deployed.  You might be wondering what an appropriate Unit Test percentage coverage is.  Well the answer is - IT DEPENDS.  This will again depend on your team's specific make-up on team and/or developer maturity.  I found [this discussion on StackOverflow](https://stackoverflow.com/a/90021) nails this.  In simple terms, a random number percentage (%) does not equal good code coverage.  Tools such as [SonarQube](https://www.sonarsource.com/products/sonarqube/) can help here. increasingly, code quality can also be increased with artificial intelligence (AI) tools such as [GitHub Copilot](https://github.com/features/copilot).

## Integration Testing
Integration testing ensures that all components work together as intended in any environment, which should be carbon copies of one another for predictability, stability and reliability.  For example, in a web application scenario, integration testing ensures that user interfaces interact correctly with a RESTful API, which, in turn, communicates seamlessly with the underlying database. Testing might involve validating data flow, error handling, and overall system behavior to guarantee cohesion and reliability across the entire stack.

## Performance and Load Testing
Here we need to make sure to test throughput of an application.  This ivolves testing CPU load, memory (RAM) load, response times and service level agreements (SLAs) are met.  For example, evaluating how the web application performs under a specified number of concurrent users, how the REST API responds to increased requests, and how the database manages a higher volume of transactions. This testing ensures that the different components can sustain optimal performance and reliability under varying load conditions, helping identify and address potential bottlenecks. Here we can use tools such as Apache [JMeter](https://jmeter.apache.org/), [Loadrunner](https://www.microfocus.com/en-us/portfolio/performance-engineering/overview) and [Grafana k6](https://k6.io/).

## Vulnerability Testing
Here we introduce Dev**Sec**Ops, which integrates security practices seamlessly into the DevOps pipeline. It encompasses three major areas outlined in the table below.

DevSecOps Area | Description | Tooling
---|---|---
Dependency checks / Open Source Scanning | Examining and securing external software dependencies, such as code from third party sources | [Artifactory](https://jfrog.com/artifactory/), [OWASP Dependency-Check](https://owasp.org/www-project-dependency-check/), [National Vulnerability Database (NVD)](https://nvd.nist.gov/)
Static Application Security Testing (SAST) | Identifying vulnerabilities in the source code | [Checkmarx](https://checkmarx.com/), [Fortify](https://www.microfocus.com/en-us/cyberres/application-security/static-code-analyzer), [GitHub CodeQL](https://codeql.github.com/)
Dynamic Application Security Testing (DAST) | Assessing security during runtime | [OWASP ZAP](https://www.zaproxy.org/), [Burp Suite](https://portswigger.net/burp), [Acunetix](https://www.acunetix.com/)


## Rollbacks and Zero-downtime Releases
Automated rollbacks are crucial for maintaining system stability and resilience and enable zero-downtime releases. Techniques such as rolling updates, blue/green deployments, canary releases, and feature flags contribute to the success of automated rollbacks. 

- Rolling updates involve gradually replacing instances of the application with new versions, minimizing downtime - see [Kubernetes - Performing a Rolling Update](https://kubernetes.io/docs/tutorials/kubernetes-basics/update/update-intro/).
- Blue/green deployments enable switching between two environments (blue for the existing version, green for the new one) to facilitate seamless rollbacks.
- Canary releases deploy changes gradually to a subset of users for early validation.
- Feature flags allow toggling specific features on or off, providing the ability to quickly disable problematic functionalities - see tools like [LaunchDarkly](https://launchdarkly.com/) or [Flagsmith](https://www.flagsmith.com/).

## Automated Change Orders
This one is pretty simple - avoid manual committees and boards for approvals.  This creates waste and decreases efficiency, speed and does not create knowledge through testing.

# Final Thoughts
We discussed the concept of DevOps gates in the context of a continuous integration and continuous deployment (CI/CD) pipeline. We also explored the importance of implementing gates to ensure quality, security, and compliance at different stages of the development process. Above, I also provided a comprehensive list of DevOps gating, to enhance  the efficiency and reliability of the software delivery pipeline.
