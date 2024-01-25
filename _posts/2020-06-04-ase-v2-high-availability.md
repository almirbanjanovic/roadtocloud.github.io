---
title:  "Azure App Service Environment (ASEv2) High Availability"
header:
  teaser: "/assets/images/ase-v2-high-availability.png"
excerpt: "Increasing ASEv2 SLA from 99.5% to 99.99% and ensuring resilience and minimal downtime."
tags:
  - architecture
  - cloud
---

Out of the box, Microsoft guarantes an SLA, or availability, of 99.95% for Azure App Services and Azure App Service Environments.  This becomes problematic for applications which require an SLA of at least 99.99% or more.  The trick here is to use Application Gateways and Availability Zones to increase availability and resilience.  The diagram below showcases such a deployment.  

<figure>
	<a href="/assets/images/ase-v2-high-availability.png"><img src="/assets/images/ase-v2-high-availability.png"></a>
</figure>

However, the obvious downside for such an architecture would be the increased cost of a duplicate deployment in a separate Availability Zone.
