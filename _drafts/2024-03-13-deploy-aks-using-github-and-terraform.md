---
title:  "Deploy Azure Kubernetes Service (AKS) using GitHub and Terraform"
header:
  #teaser: "/assets/images/github_terraform_azure_function.jpg"
excerpt: "Let's deploy Azure Kubernetes Service (AKS) using GitHub and Terraform!"
toc: true
tags:
  - Cloud
  - Programming
  - DevOps
---

# Overview

This post will demonstrate the deployment of an Azure Kubernetes Service using Terraform and GitHub Actions for CI/CD.  The full code can be found in my GitHub [azure-kubernetes-service](https://github.com/rimlaban7/azure-kubernetes-service-terraform) repository. For this project we'll also make sure GitHub and Azure use OpenID Connect (OIDC) for authenticating service principals. This will save us from storing client secrets (basically passwords).  For an overview of how this works, see my blog post titled [Authenticating GitHub and Azure DevOps using OpenID Connect ](https://www.theroadtocloud.com/blog/github-and-azure-devops-oidc-authentication/).

# Prerequisites

Before you begin, you'll need to have the following:

- An Azure subscription.
- A GitHub account.
- Your favorite IDE - I prefer Visual Studio Code with the below extensions installed
    - GitHub Actions
    - HashiCorp HCL 
    - HashiCorp Terraform

# Configuration

## Azure, Entra, GitHub

We'll need to create an Entra application and service principal that has the appropriate permissions to create and modify Azure resources.  There are a few different ways to accomplish this depending on you subscription type and preferred method (Azure Portal, Azure CLI, or PowerShell).  For this project, I followed this guide: [Configuring OpenID Connect in Azure](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure).
