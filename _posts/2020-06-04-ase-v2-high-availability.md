---
title:  "Azure App Service Environment (ASEv2) High Availability"
header:
  teaser: "/assets/images/ase-v2-high-availability.png"
excerpt: "Increasing cloud application resilience in Microsoft Azure with Traffic Managers, Application Gateways and App Services."
tags:
  - architecture
  - cloud
---

Out of the box, Microsoft guarantes an SLA, or availability, of 99.95% for Azure App Services and Azure App Service Environments.  This becomes problematic for applications which require an SLA of at least 99.99% or more.  The trick here is to use Application Gateways and Availability Zones to increase availability and resilience.  The diagram below showcases such a deployment.  

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/ase-v2-high-availability.png){: .align-center}

However, the obvious downside for such an architecture would be the increased cost of a duplicate deployment in a separate Availability Zone.
