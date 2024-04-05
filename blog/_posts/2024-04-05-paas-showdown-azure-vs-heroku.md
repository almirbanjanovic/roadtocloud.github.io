---
title:  "PaaS Showdown: Choosing a Hosting Platform for a Ruby on Rails Application"
header:
  teaser: "/assets/images/azure-vs-heroku.png"
excerpt: "Which platform choice is better for hosting applications - Azure or Heroku?  Let's dive in and find out!"
#classes: wide
toc: true
tags:
  - Cloud
---

<figure style="width: 250px" class="align-center">
	<a href="/assets/images/azure-vs-heroku.png"><img src="/assets/images/azure-vs-heroku.png"></a>
</figure>

# Introduction
Nowdays there are many ways to host your web app ranging from containerization for portability to full-fledged IaaS (Infrastructure as a Service) deployments.  However, these two options come with significant management overhead, which can be quite challenging.  Another solution is choosing a platform, which manages this for you; this is oftern referred to as Platform as a Service (PaaS).  Two popular choices represent Azure App Services and Heroku.  However, the choice between these two depends on individual application as well as business requirements. Let's assume this scenario: a team is using Azure currently, but wants to consider Heroku.  Let's take a look at how this shakes out!

# Requirements
## In Scope
How would you begin by analyzing a showdown between Azure App Services and Heroku?  Obviously, we'll need to settle on some requirements.  For this, let's consider two important benchmarks: security and performance. This is a business critical application so performance is paramount.  As a result, we also require high availability. We'll also need to make sure to host our database in Azure. Sensitive data will be flowing, so we'll need to take that into account.

## Additional Assumptions
Let's assume this Ruby on Rails application requires pretty significant up-time.  As a result, this business critical application requires High Availability (HA). 
Additionally, usually it is best practice for companies to advertise one IP address to the world.  Multiple IP addresses can present challenges and unnecessary security risks.  This analysis also assumes that SNAT (Source Network Address Translation) is required, so that only one IP address is advertised. This will avoid exposing additional attack vectors.
Finally, one last assumption: This is a standard 3-tier app with a web front-end, an API middle tier, and a back-end database tier.

## Out of Scope
For this analysis I have ommitted focusing on DevOps and CI/CD and how that could affect the outcome.  Both platforms offer support for Git and should offer support for popular code repositories like GitHub.  However, this adds an aditional layer of complexity and takes away from the main concerns of analyzing the impact on performance and security. DevOps and CI/CD practices wouldn't necessarily change this analysis focused on security and performance.

Similarly, I have ommitted disaster recovery (DR), as this, again, introduces an additional layer of complexity and would require global load balancers (like Traffic Manager). This takes away from the main two requirements of analyzing security and performance. This would not change the analysis on performance and security.

Finally, I have also ommitted showing a CDN, which, for a critical application should probably be part of the architecture.

# Option 1: Azure

## Architecture

First, we'll require a Hub-and-Spoke architecture.  Most LOB (line of business) applications should be separated into their own subscriptions and as a result their own virtual networks (VNets).  This creates an additional layer of security as it makes a blast radius of an attack smaller. Take a look at the [Azure's Cloud Adoption Framework Landing Zones](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/) for a more detailed deep dive on this. For this architucture everything is hosted in Azure, including the Ruby application.  For this, we should utilize an isolated [Internal Load Balancer (ILB) Application Serivce Environment (ASE)](https://learn.microsoft.com/en-us/azure/app-service/environment/overview). App Services in an isolated environment can host a Ruby on Rails application either as a Linux web app or inside a Docker container. ASEs are great for workloads that require
- High scale
- Isolation and secure network access
- High memory utilization
- High requests per second (RPS). You can create multiple App Service Environments in a single Azure region or across multiple Azure regions. This flexibility makes an App Service Environment ideal for horizontally scaling stateless applications with a high RPS requirement.

Additionally, to further keep traffic private, we'll utilize Private Endpoints to connect to Azure SQL.  This combination offers full control of traffic flow, and is completely isolated over private network connections within a single LOB VNet with private IPs. This demonstrates network enclaving and offers protection and reduces the blast radius should an attack by a malicious actor occur.

<figure>
    <a href="/assets/images/paas-showdown-azure.png"><img src="/assets/images/paas-showdown-azure.png"></a>
</figure>

## Pros
- Team is familiar with and currently uses Azure
- Network enclaving reduces risk
- Fine-grained security control, fewer attack vectors for malicious actors
- Better performance, especially when both application and database are hosted on Azure
- Integration with Microsoft Ecosystem

## Cons
- Infrastructure complexity
- Management 

# Option 2: Heroku

## Architecture

The combination of Heroku application hosting and Azure database hosting creates additional complexity.  For security, right out of the gate we want to use Heroku Shield Spaces.  This will offer isolation and security similar to Azure Application Service Environments (ASEs) for isolated hosting.  Here are the major selling points for [Heroku Shield](https://www.heroku.com/shield):
- Dedicated environment for high compliance apps
- Ability to sign BAAs for HIPAA compliance
- PCI compliance
- Keystroke logging
- Space level log drains
- Strict TLS enforcement

Additionally, we'll need to secure traffic in transit between our Ruby on Rails application hosted in Heroku and our database hosted in Azure.  For this, we'll use a Site-to-Site VPN.  Heroku offers this very capability with [Shield Space VPN Connections](https://devcenter.heroku.com/articles/private-space-vpn-connection). This VPN tunnel offers redundancy and IPSec encryption. (Another option would be to use HTTPS/TLS encrypted traffic, but this would not only introduce additional latency, it would also let our sensitive data flow accross the internet.  This is the least desirable option and I would not recommend this.)

Finally, we'll also need to still utilize the same Site-to-Site VPN tunnel for egress traffic from Heroku through our Azure VNet in order to only expose one public business IP address using SNAT.

<figure>
    <a href="/assets/images/paas-showdown-heroku.png"><img src="/assets/images/paas-showdown-heroku.png"></a>
</figure>

## Pros
- Managed infrastructure offers simplicity
- Developers can focus on writing code

## Cons
- Limited control due to managed infrastructure, but [Heroku Shield](https://www.heroku.com/shield) is offered for high compliance applications
- Complicated network routing with additional hops and points of failure
- Site-to-Site VPN creates additional attack vector for malicious actors
- Degraded performance due to need to use Site-to-Site VPN introduces additional latency and will degrade performance

# Recommendation

