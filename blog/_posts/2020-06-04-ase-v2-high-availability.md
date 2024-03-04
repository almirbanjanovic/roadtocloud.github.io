---
title:  "Azure App Service Environment (ASEv2) High Availability"
header:
  teaser: "/assets/images/ase-v2-high-availability.png"
excerpt: "Increasing ASEv2 SLA from 99.95% to 99.99% and ensuring resilience and minimal downtime."
tags:
  - Architecture
  - Cloud
---

Out of the box, Microsoft guarantes an SLA, or availability, of 99.95% for Azure App Services and Azure App Service Environments (ASEs).  This may be more than adequate for many workloads, but becomes problematic for critical applications which require an SLA of at least 99.99% or more.  I worked on a project for a Fortune 50 company with exactly this dilemma. The trick here is to use Application Gateways and Availability Zones to increase availability and resilience.  The diagram below showcases such a deployment.  Using Azure Resource Manager (ARM) Templates, you can specify to "pin" an ASE to a certain Availability Zone.  Then, duplicate that same architecture in a different Availability Zone.  Use Application Gateways to route traffic between App Services in both Availability Zones.

<figure>
	<a href="/assets/images/ase-v2-high-availability.png"><img src="/assets/images/ase-v2-high-availability.png"></a>
</figure>

However, the obvious downside for such an architecture would be the increased cost of a duplicate deployment in a separate Availability Zone.
