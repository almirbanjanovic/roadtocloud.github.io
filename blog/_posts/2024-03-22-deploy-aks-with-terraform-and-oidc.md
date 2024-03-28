---
title:  "Deploy Azure Kubernetes Service (AKS) using GitHub and Terraform"
header:
  teaser: "/assets/images/aks-terraform-oidc.svg"
excerpt: "Let's deploy Azure Kubernetes Service (AKS) using GitHub and Terraform!"
classes: wide
toc: true
tags:
  - Cloud
  - Programming
  - DevOps
---

<figure class="align-left">
	<a href="/assets/images/aks-terraform-oidc.svg"><img src="/assets/images/aks-terraform-oidc.svg"></a>
</figure>

# TL;DR
If you wanna jump straight into it, check out my GitHub repository for this project [here](https://github.com/rimlaban7/azure-kubernetes-service-terraform).  This includes a step-by-step guide, including prerequisites, configuration and deployment of AKS using OIDC, Terraform, GitHub Actions.  The goal of this project is to demonstrate the three (3) main points below:

1. Use of OIDC for Microsoft Entra and GitHub Actions authentication,
2. Use of Azure Storage Account as Terraform Backend for state storage, and
3. Deployment of minimum viable product (MVP) Azure Kubernetes Service (AKS).
<br>

# Flexibility, Portability and Security with Kubernetes, Terraform, GitHub, and OpenID Connect (OIDC)

Let's talk about something that sounds complicated but is actually not as bad as it might seem: deploying Azure Kubernetes Service (AKS) using Terraform, orchestrated through GitHub Actions. And the cherry on top? Let's make this entire process seamless and more secure by integrating OpenID Connect (OIDC) for authentication. This will give your developer experience a major upgrade!  

## Keeping It Simple with Trunk-Based Development

For this project, I kept is simple with a Trunk-Based Development strategy. Simply put, it honestly makes the most sense for a single person, or a very small highly skilled team working on a project.  For a complete overview of all the different ways you can structure your repositories and workflow strategies, see my blog post on [Branching Strategies](https://www.theroadtocloud.com/blog/branching-strategies/).

## Goodbye Passwords, Hello OIDC

Imagine deploying infrastructure without worrying about safeguarding a pile of secrets or passwords. From an engineer's or software developer's lense, this presents a host of challenges.  How do you store, manage and rotate those secrets and passwords? Thanks to OIDC, that daunting endeavor is a thing of the past! OIDC simplifies authenticating with Azure, meaning less time fretting over security and more time doing what you love: crafting great code. It’s a smarter, streamlined way to work, and honestly, who doesn’t want that?  For more information on how OIDC works with Entra, Azure, GitHub and Azure DevOps, see my blog post titled [Authenticating GitHub and Azure DevOps using OpenID Connect](https://www.theroadtocloud.com/blog/github-and-azure-devops-oidc-authentication/).  This project uses OIDC. It just makes sense.

## Flexibility and Portability 

### Kubernetes
Why make a big deal about Kubernetes, or K8s as the cool kids call it? Simply put, it gives you the flexibility to manage your applications with ease, regardless of where you choose to run them. The major Cloud Service Providers (CSPs) have all jumped on board - Microsoft with Azure Kubernetes Service (AKS), AWS with Elastic Kubernetes Service (EKS), and Google Cloud Platform (GCP) with Google Kubernetes Service (GKS). Think of K8s as your cloud Swiss Army knife, ready to adapt to whatever your project needs. K8s represents a cloud-agnostic service, pitting the major CSPs against each other to earn your business.  Competition is a good thing for the consumer (i.e. you and me!).

### Terraform
Finally, what's all the Terraform fuss about?  Some might say - can't I just use Azure Resource Manager (ARM) Templates?  Well, technically, yes.  But when you learn Terraform, a cross-CSP Infrastructure as Code (IaC) declarative deployment tool, it enables flexibility for IaC just like K8s does for application hosting.  A CSP decides to raise prices for infrastrucuture?  Don't fret, take your Terraform and Kubernetes knowledge to another vendor! Once again, competition is the name of the game.  Let' the CSPs compete for your business!

# Why This All Matters

Let's make sure we hit the point of this post home.  In the grand scheme of things, embracing tools such as OIDC, Kubernetes, Terraform and GitHub is about remaining flexible and secure in a technology landscape that is evolving at a rapid pace. It's about ensuring that as developers, architects, or managers, we’re not just keeping pace but setting the pace, ready to adapt and thrive no matter what comes our way.

So, here’s to making deployment a smoother, more secure part of our development journey. Ready to take the next step? Let’s dive in and see just how much easier and more enjoyable your work can become.  Check out the entire repo [here](https://github.com/rimlaban7/azure-kubernetes-service-terraform)!


