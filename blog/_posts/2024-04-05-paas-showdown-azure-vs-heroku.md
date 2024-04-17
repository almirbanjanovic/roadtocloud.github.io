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

<figure style="width: 350px" class="align-left">
	<a href="/assets/images/azure-vs-heroku.png"><img src="/assets/images/azure-vs-heroku.png"></a>
	<figcaption>Created with DALL-E 3</figcaption>
</figure>

# Introduction
Nowdays there are many ways to host your web app ranging from containerization for portability to full-fledged IaaS (Infrastructure as a Service) deployments.  However, these two options come with significant management overhead, which can be quite challenging.  Another solution is choosing a platform, which manages this for you; this is often referred to as Platform as a Service (PaaS).  Two popular choices represent Azure and Heroku.  However, the choice between these two depends on individual application as well as business requirements. Let's assume this scenario: a team is using Azure currently, but wants to consider Heroku.  Let's take a look at how this shakes out!

# Requirements
## In Scope
How would you begin by analyzing a showdown between and Heroku?  Obviously, we'll need to settle on some requirements.  For this, let's consider two important benchmarks: security and performance. This is a business critical application so performance is paramount.  We'll also need to make sure to host our database in Azure. Sensitive data will be flowing, so we'll need to take that into account.

## Additional Assumptions
Let's assume this Ruby on Rails application requires pretty significant up-time.  As a result, this business critical application requires High Availability (HA). 
Additionally, usually it is best practice for companies to advertise one IP address to the world.  Multiple IP addresses can present challenges and unnecessary security risks.  This analysis also assumes that SNAT (Source Network Address Translation) is required, so that only one IP address is advertised. This will avoid exposing additional attack vectors.
Finally, one last assumption: this is a standard 3-tier app with a web front-end, an API middle tier, and a back-end database tier.

## Out of Scope
For this analysis I have ommitted focusing on DevOps and CI/CD and how that could affect the outcome.  Both platforms offer support for Git and should offer support for popular code repositories like GitHub. However, this adds an aditional layer of complexity and takes away from the main concerns of analyzing the impact on performance and security. DevOps and CI/CD practices wouldn't necessarily change this analysis focused on security and performance.

Similarly, I have ommitted disaster recovery (DR), as this, again, introduces an additional layer of complexity and would require global load balancers (like Traffic Manager). This takes away from the main two requirements of analyzing security and performance. This would not change the analysis on performance and security.

Finally, I have also ommitted showing a Content Delivery Network (CDN) for cashing, which, for a critical application should probably be part of the architecture.

# Option 1: Azure

## Architecture Option 1

First, we'll require a Hub-and-Spoke architecture.  Most LOB (line of business) applications should be separated into their own subscriptions and as a result their own virtual networks (VNets).  This creates an additional layer of security as it makes a blast radius of an attack smaller. Take a look at the [Azure's Cloud Adoption Framework Landing Zones](https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/) for a more detailed deep dive on this. The only public endpoint will be a public IP attached to an Application Gateway (Azure's Layer 7 load balancer), which will use the WAFv2 SKU to protect incoming web traffic.  For application hosting, we should utilize an isolated [Internal Load Balancer (ILB) Application Serivce Environment (ASE)](https://learn.microsoft.com/en-us/azure/app-service/environment/overview). This will ensure private traffic for security.  App Services, Azure's PaaS option, in an isolated environment can host a Ruby on Rails application either as a Linux web app or inside a Docker container. ASEs are great for workloads that require
- High scale
- Isolation and secure network access
- High memory utilization
- High requests per second (RPS). You can create multiple App Service Environments in a single Azure region or across multiple Azure regions. This flexibility makes an App Service Environment ideal for horizontally scaling stateless applications with a high RPS requirement.

Additionally, to further keep traffic private, we'll utilize Private Endpoints to connect to an Azure SQL database.  This combination offers full control of traffic flow, and is completely isolated over private network connections within a single LOB VNet with private IPs. This demonstrates network enclaving and offers protection and reduces the blast radius should an attack by a malicious actor occur.

Finally, in order to satisfy our HA requirement for this business-critical application, we'll utilize zone-redundant deployments for all of our resources.

<figure>
    <a href="/assets/images/paas-showdown-azure.png"><img src="/assets/images/paas-showdown-azure.png"></a>
	<figcaption>(Click to enlarge!)</figcaption>
</figure>

### Pros
- Team is familiar with and currently uses Azure
- Network enclaving increases security and reduces risk
- Fine-grained security control
- Better performance, especially when both application and database are hosted on Azure
- Integration with Microsoft Ecosystem

### Cons
- App Service support for Ruby has ended, requiring containerization
- Infrastructure complexity
- Management overhead

## Architecture Option 2
Another similar option to ASEs represents Azure Container Apps (ACA).  Ever wanted to deploy your containers to Kubernetes but found all the plumbing too complicated?  This is where ACA comes in as a managed Kubernetes platform.  Whereas Azure Kubernetes Service (AKS) only abstracts away the Kubernetes control plane, ACA takes it a step further and also abstracts away everything else.  ACA represents a fully managed Kubernetes PaaS.  Most of the architecture will be similar to our ASE architecture above, but now instead of App Services, we are using ACA containers.  The architecture appears simpler, cleaner. We maintain security through enclaving and deploying the ACA into an existing VNet with existing security controls.  For more information on this see [Azure Container Apps Environment Security](https://learn.microsoft.com/en-us/azure/container-apps/networking?tabs=workload-profiles-env%2Cazure-cli#environment-security).

<figure>
    <a href="/assets/images/paas-showdown-azure-aca.png"><img src="/assets/images/paas-showdown-azure-aca.png"></a>
	<figcaption>(Click to enlarge!)</figcaption>
</figure>

### Pros
- Team is familiar with and currently uses Azure
- Network enclaving increases security and reduces risk
- Fine-grained security control
- Better performance, especially when both application and database are hosted on Azure
- Integration with Microsoft Ecosystem
- Includes functionality for auto-scaling and versioning

### Cons
- Kubernetes-specific knowledge still required to reap most benefits
- Infrastructure complexity
- Management overhead

# Option 2: Heroku

## Architecture Option 1

The combination of Heroku application hosting and Azure database hosting creates additional complexity.  For security, right out of the gate we want to use Heroku Shield Spaces.  This will offer isolation and security similar to Azure Application Service Environments (ASEs) or Azure Container App (ACA) Environments for isolated hosting.  Here are the major selling points for [Heroku Shield](https://www.heroku.com/shield):
- Dedicated environment for high compliance apps
- Ability to sign BAAs for HIPAA compliance
- PCI compliance
- Keystroke logging
- Space level log drains
- Strict TLS enforcement

However, we still need to ensure network enclaving for Azure and individual VNets for LOB applications.  Additionally, we'll need to secure traffic in transit between our Ruby on Rails application hosted in Heroku and our database hosted in Azure.  For this, we'll use a Site-to-Site VPN at a minimum.  Heroku offers this very capability with [Shield Space VPN Connections](https://devcenter.heroku.com/articles/private-space-vpn-connection). This VPN tunnel offers redundancy and IPSec encryption. (Another option would be to use HTTPS/TLS encrypted traffic, but this would not only introduce additional latency, it would also let our sensitive data flow accross the internet.  This is the least desirable option and I would not recommend this.)

Finally, we'll also need to still utilize the same Site-to-Site VPN tunnel for egress traffic from Heroku through our Azure VNet in order to only expose one public business IP address using SNAT.

<figure>
    <a href="/assets/images/paas-showdown-heroku.png"><img src="/assets/images/paas-showdown-heroku.png"></a>
	<figcaption>(Click to enlarge!)</figcaption>
</figure>

### Pros
- [Heroku Shield](https://www.heroku.com/shield) is offered for high compliance applications
- Ruby applications can run natively and do not require containerization
- Managed infrastructure offers simplicity
- Applications can be up and running fast

### Cons
- Limited control due to managed infrastructure
- Complicated network routing with additional hops and points of failure between Heroku and Azure
- Degraded performance due to additional Site-to-Site VPN hops, which introduce additional latency
- Increased security risk because traffic flows over open internet, even though it is encrypted over a VPN tunnel
- Team has not worked with Heroku
- Potential for vendor lock-in

## Architecture Option 2

But what would our Heroku architecture look like if both our web app *and* database were hosted in Heroku? As can be seen in the diagram below, this represents a massive simplification of the architecture.  Not only do we drop the need to containerize our Ruby on Rails application as is the case with Azure PaaS services, but now we can also utilize [Heroku Postgres for Shield Spaces](https://devcenter.heroku.com/articles/heroku-postgres-and-private-spaces).  Our entire architecture can now be hosted in Heroku, inside an isolated, secure environment built specifically for high compliance applications.

<figure>
    <a href="/assets/images/paas-showdown-heroku-db.png"><img src="/assets/images/paas-showdown-heroku-db.png"></a>
	<figcaption>(Click to enlarge!)</figcaption>
</figure>

### Pros
- [Heroku Shield](https://www.heroku.com/shield) is offered for high compliance applications
- Ruby applications can run natively and do not require containerization
- Managed infrastructure offers simplicity
- Applications can be up and running fast
- Heroku Postgres for Shield Spaces addition massively simplifies architecture

### Cons
- Limited control due to managed infrastructure
- Team has not worked with Heroku
- Potential for vendor lock-in

# Recommendation

The choice between Azure and Heroku application hosting for Ruby on Rails depends on project size, complexity, priorities, level of needed control and business direction. Azure offers ASEs and ACAs as PaaS options for performance and high scale through isolation.  This is ideal for larger, complex applications requiring flexibility, scalability and fine-grained control.   Similarly, a team's familiarity and built-out processes currently existing with Azure should not be discounted, because migrating those to Heroku could introduce additional cost and risk.

However, Heroku could offer faster application deployments for tasks such as prototyping and proof of concepts. Heroku is great for simplicity and fast deployment, because it does not require containerization for Ruby on Rails applications. In addition, if we drop the requirement to keep data on Azure, Heroku offers a clear choice - it massively simplifies the cloud architecture and maintainability. However, if there is a hard requirement to keep data within Azure, this creates additional complexity and really handicaps Heroku as a choice, because a Site-to-Site VPN would degrade performance and decrease security.

In the end, if a team currently uses containers to deploy a Ruby on Rails application, and absolutely *must* maintain data within Azure, I would stick to either Azure PaaS offering for web app hosting - Azure Application Service Environments or Azure Container Appps. The added overhead of setting up and maintaining a Site-to-Site VPN between Heroku and Azure really opens up security risks and decreases performance. However, without the data in Azure requirement, Heroku would present a clear winner and a much better choice for developer experience, decreases management overhead and deployment simplicity.  The choice depends on data hosting preference, future business direction and a more detailed price comparison.
