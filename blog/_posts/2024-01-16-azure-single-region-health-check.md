---
title:  "Custom Health-Checks for a Hybrid Cloud Application"
header:
  teaser: "/assets/images/ase-single-region-health-check.png"
excerpt: "Increasing hybrid cloud application resilience in Microsoft Azure with Traffic Managers, Application Gateways and App Services."
tags:
  - architecture
  - cloud
---

In an active-passive dual-region architecture on Microsoft Azure, key services like Application Service Environment (ASE), Azure App Services, Azure Traffic Manager, and Azure Application Gateway can ensure resilience. However, what if you introduce on-prem infrastructure for a hybrid cloud environment?  Some critical applications are not migrated fully to a cloud environment and operate from both.  This is where it becomes complicated to ensure high availability.  A solution presents itself in creating a simple custom healthcheck page to check health of each downstream endpoint.  Then, point the Traffic Manager's healthcheck probe against this website, which should be deployed onto the an App Service.  This should return an appropriate HTTP response based on its internal logic checks (whether a downstream endpoint, or a combination thereof is up or down).  Regular health-checks are crucial for maintaining continuous availability and optimal performance.

<figure>
    <a href="/assets/images/ase-single-region-health-check.png"><img src="/assets/images/ase-single-region-health-check.png"></a>
</figure>

Conducting health-checks helps identify and address issues proactively, minimizing downtime and ensuring a resilient infrastructure. This is essential for business continuity, emphasizing the importance of a robust and reliable cloud setup.


