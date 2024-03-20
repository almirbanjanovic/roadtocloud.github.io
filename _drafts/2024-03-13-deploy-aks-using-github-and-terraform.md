---
title:  "Deploy Azure Kubernetes Service (AKS) using GitHub and Terraform"
header:
  #teaser: "/assets/images/github_terraform_azure_function.jpg"
excerpt: "Let's deploy Azure Kubernetes Service (AKS) using GitHub and Terraform!"
classes: wide
toc: true
tags:
  - Cloud
  - Programming
  - DevOps
---

# Deploying AKS with Terraform, GitHub, and OpenID Connect (OIDC)

Let's talk about something that sounds complicated but is actually not as bad as it might seem: deploying Azure Kubernetes Service (AKS) using Terraform, orchestrated through GitHub Actions. And the cherry on top? Let's make this entire process seamless and more secure by integrating OpenID Connect (OIDC) for authentication. This will give your developer experience a major upgrade!  

## TL;DR
If you wanna jump straight into it, check out my GitHub repository for this project [here](https://github.com/rimlaban7/azure-kubernetes-service-terraform).

## Goodbye Passwords, Hello OIDC

Imagine deploying infrastructure without worrying about safeguarding a pile of secrets or passwords. From an engineer's or software developer's lense, this presents a host of challenges.  How do you store, manage and rotate those secrets and passwords? Thanks to OIDC, that daunting endeavor is a thing of the past! OIDC simplifies authenticating with Azure, meaning less time fretting over security and more time doing what you love: crafting great code. It’s a smarter, streamlined way to work, and honestly, who doesn’t want that?  For more information on how OIDC works with Entra, Azure, GitHub and Azure DevOps, see my blog post titled [Authenticating GitHub and Azure DevOps using OpenID Connect](https://www.theroadtocloud.com/blog/github-and-azure-devops-oidc-authentication/).

## Flexibility and Portability Matter

Why make a big deal about Kubernetes (or K8s as the cool kids call it)? Simply put, it gives you the flexibility to manage your applications with ease, regardless of where you choose to run them. The major Cloud Service Providers (CSPs) have all jumped on board - Microsoft with Azure Kubernetes Service (AKS), AWS with Elastick Kubernetes Service, and Google Cloud Platform (GCP) with Google Kubernetes Service (GKS). Think of K8s as your cloud Swiss Army knife, ready to adapt to whatever your project needs. K8s represents a cloud-agnostic service, pitting the major CSPs against each other to earn your business.

## The Deployment Journey

Here's how we demystify the whole process: start with a solid foundation (our GitHub repo, prepped and ready, following top-notch practices). Then, we walk you through the setup like a friend who’s been there before, covering everything from the nooks and crannies of OIDC configuration to the secrets of setting up GitHub like a treasure trove of your most prized assets. And yes, we even touch on initializing Terraform – think of it as laying down the tracks for your deployment train to glide smoothly.
Keeping It Simple with Trunk-Based Development

We keep our development process straightforward with a Trunk-Based Development strategy. It’s about having a single, main line of work that keeps everyone on the same page, reducing complexity and fostering faster, more agile releases. Add GitHub Actions into the mix, and you've got a recipe for deploying with confidence and speed.
Wrapping Up: Deploying with Confidence

As we wrap this up, think of the deployment process not as a daunting task but as your next step towards mastering the cloud. With the security of OIDC, the flexibility of Kubernetes, and the simplicity of our approach, you're well on your way to deploying AKS like a pro. It’s about making your life easier, your work more secure, and your deployments a reason to celebrate.
Why This All Matters

In the grand scheme of things, embracing these tools and strategies is about staying agile and secure in a world that’s constantly changing. It's about ensuring that as developers, architects, or managers, we’re not just keeping pace but setting the pace, ready to adapt and thrive no matter what comes our way.

So, here’s to making deployment a smoother, more secure part of our development journey. Ready to take the next step? Let’s dive in and see just how much easier and more enjoyable your work can become.


