---
title:  "PaaS Showdown: Choosing a Hosting Platform for a Ruby on Rails Application"
header:
  teaser: "/assets/images/azure-vs-heroku.png"
excerpt: "Which platform choice is better for hosting applications - Azure or Heroku?  Let's dive in and find out!"
classes: wide
toc: true
tags:
  - Cloud
---

<figure class="align-left">
	<a href="/assets/images/azure-vs-heroku.png"><img src="/assets/images/azure-vs-heroku.png"></a>
</figure>

# Introduction
Nowdays there are many ways to host your web app ranging from containerization for portability to full-fledged IaaS (Infrastructure as a Service) deployments.  However, these two options come with significant management overhead, which can be quite challenging.  Another solution is choosing a platform, which manages this for you; this is oftern referred to as Platform as a Service (PaaS).  Two popular choices represent Azure App Services and Heroku.  However, the choice between these two depends on individual application as well as business requirements.  Let's take a look at how this shakes out!

# Requirements
## In Scope
How would you begin by analyzing a showdown between Azure App Services and Heroku?  Obviously, we'll need to settle on some requirements.  For this, let's consider two important benchmarks: security and performance. We'll also need to make sure to host our database in Azure.  

## Additional Assumptions
First, we'll assume we require a Hub-and-Spoke architecture.  Most LOB (line of business) applications should be separated into their own subscriptions and as a result their own virtual networks (VNets).  This creates an additional layer of security as it makes a blast radius of an attack smaller.  
Let's also assume this Ruby on Rails application requires pretty significant up-time.  As a result, this business critical application requires High Availability (HA). 
Additionally, usually it is best practice for companies to advertise one IP address to the world.  Multiple IP addresses can present challenges and unnecessary security risks.  This analysis also assumes that SNAT (Source Network Address Translation) is required, so that only one IP address is advertised. This will avoid exposing additional attack vectors.
Finally, one last assumption: This is a standard 3-tier app with a web front-end, an API middle tier, and a back-end database tier.

## Out of Scope
For this analysis I have ommitted focusing on DevOps and CI/CD and how that could affect the outcome.  Both platforms offer support for Git and should offer support for popular code repositories like GitHub.  However, this adds an aditional layer of complexity and takes away from the main concerns of analyzing the impact on performance and security.

Similarly, I have ommitted disaster recovery (DR), as this, again, introduced an additional layer of complexity and introduce global load balancers (like Traffic Manager) and takes away from the main two requirements of analyzing security and performance.

Finally, I have also ommitted showing a CDN, which, for a critical application should probably be part of the architecture.

# Option 1: Azure

## Architecture

## Pros
- Fine-grained security control for absolute control
- Better Performance, especially when both application and database are hosted on Azure
- Integration with Microsoft Ecosystem

## Cons
- Complexity
- Management 

# Option 2: Heroku

## Architecture

## Pros
- Managed infrastructure offers simplicity
- Developers can focus on writing great code

## Cons
- Cost at scale
- Limited control over security due to shared and hidden infrastructure
- Site-to-Site VPN creates additional attack vector for malicious actors
- Site-to-Site VPN introduces additional latency and will degrade performance

# Recommendation

