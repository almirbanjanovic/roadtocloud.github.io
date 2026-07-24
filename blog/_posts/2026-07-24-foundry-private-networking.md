---
title: "Private Networking for Microsoft Foundry Hosted Agents"
date: 2026-07-24
header:
  teaser: "/assets/images/ai-foundry-private-networking.png"
excerpt: "Private networking for Microsoft Foundry Hosted Agents is one of those areas where the architecture details matter more than the demo."
toc: true
classes: wide
categories:
  - blog
tags:
  - ai
  - agents
  - foundry
  - azure
  - private-networking
  - bicep
  - terraform
  - devops
---

<figure>
    <a href="/assets/images/ai-foundry-private-networking.png"><img src="/assets/images/ai-foundry-private-networking.png" alt="AI Foundry private networking architecture"></a>
    <figcaption>Figure 1: Microsoft Foundry Agent Service with network injection and customer-owned stateful backing services.</figcaption>
</figure>

I have been noticing customers struggling to set up private networking for *Microsoft Foundry Hosted Agents*. Not because the idea is hard to understand, but because the real implementation touches more than just Foundry. It touches virtual networks, delegated subnets, private endpoints, private DNS, managed identity, Role-Based Access Control (RBAC), firewall rules, and whatever operating model exists between the platform team and the application team.

This is where the AI conversation sometimes gets ahead of itself. We talk about agents, tools, models and orchestration, but the old cloud fundamentals still apply. Network topology still matters. DNS still matters. Identity still matters. Infrastructure as Code (IaC) still matters. 

So I built a working example in [cloud-playground-infra](https://github.com/almirbanjanovic/cloud-playground-infra), under the [AI Foundry private networking lab](https://github.com/almirbanjanovic/cloud-playground-infra/tree/main/environments/ai-foundry). The repo has the full *Bicep* and *Terraform* implementation, so I will leave the nitty gritty deployment details there. This post is more about the pattern and why I think it matters.

## What This Is

In general terms, this is a private-networked *Foundry Agent Service* deployment with a bring-your-own (BYO) stateful stack. The Hosted Agent runtime is injected into a delegated subnet, and the stateful services — *Azure Storage*, *Azure Cosmos DB*, and *Azure AI Search* — stay in your subscription, behind private endpoints, connected through managed identity.

That is the part I care about. Agents are not just prompts. A real agent usually needs files, threads, retrieval indexes, tool outputs and durable state. Those things need to live somewhere, and if this is going into a real customer environment, "somewhere" needs to be intentional.

The pattern also follows the [Cloud Adoption Framework (CAF) landing-zone networking model](https://learn.microsoft.com/azure/cloud-adoption-framework/ready/landing-zone/design-area/network-topology-and-connectivity): networking resources in one resource group, workload resources in another. This matters because many customers operate exactly this way. The app team does not always own the VNet. The platform team often owns DNS, routing and subnet design. Same old cloud conversations, just with agents now.

## Architecture

Figure 1 shows the pattern. There is a base layer and a workload layer.

The base layer creates the VNet, delegated agent subnet, private endpoint subnets and private DNS zones. The workload layer creates the Foundry account, project, Storage account, Cosmos DB, AI Search, private endpoints, role assignments and capability hosts.

There are two paths in the repo: Bicep and Terraform. Pick one. Both deploy the same runtime architecture, but they operate differently. Bicep uses ARM deployment history. Terraform needs remote state. Nothing exotic here, but it is useful to have both because customers standardize on different IaC tools.

The most important part is DNS. A private endpoint without correct private DNS is where a lot of people get stuck. If the service Fully Qualified Domain Name (FQDN) still resolves to a public IP, you are not really on the private path. You just created a private endpoint that your client is not using.

## Public Path vs. Private Path

The lab supports two deployment modes.

The public path is useful when you are deploying from a laptop. Public network access stays enabled, but the services use default-deny firewalls with the deployer's IP temporarily allowlisted. That lets you deploy, open the portal, test the SDK, inspect storage or validate Cosmos DB without fighting the network during the first pass.

The private path is what I would expect in a more mature enterprise setup. Your deployer is already inside the network path — for example, a jumpbox, a CI/CD runner in the VNet, or a workstation connected through VPN or ExpressRoute with private DNS forwarding working correctly. In that model, there is no need to allowlist your public IP because the service FQDNs resolve to private endpoint addresses.

Neither path is "more correct" in isolation. The mistake is not knowing which one you are using.

## Why BYO State Matters

The bring-your-own stateful stack is not just there to make the diagram look more enterprise. It gives teams a clearer way to reason about where agent data lives, how it is secured, and how it is governed.

This lines up with what I wrote in [12-Factor Apps, Revisited for AI Agents](https://www.theroadtocloud.com/blog/12-factor-agents/). The agent compute should be disposable. The state around the agent should be explicit, observable and replaceable. Same discipline we have used for cloud-native apps for years. New building blocks, same principles.

It is important to remember that there is no one size fits all approach. For a quick demo, a simpler setup may be fine. For a regulated workload, customer-facing workload, or anything that needs to align with a real landing zone, I would rather start with the right boundaries than retrofit them later.

## Final Thoughts

Microsoft Foundry makes it easier to build agents, but it does not remove the need for architecture. If anything, it makes the architecture more important because agents sit between users, tools, data and downstream systems.

Private networking is one of those areas where the details matter. Subnets, private DNS, private endpoints, managed identity, RBAC propagation, temporary deployer access and teardown behavior are not the flashy parts of an AI demo, but they are the parts that determine whether the solution can survive in a real enterprise environment.

The repo is not meant to be the only way to deploy Foundry Agent Service or Hosted Agents with private networking. It is just a working pattern in both Bicep and Terraform. Use it as a starting point, mutate it for your landing zone, and test the boring parts before they become production problems.
