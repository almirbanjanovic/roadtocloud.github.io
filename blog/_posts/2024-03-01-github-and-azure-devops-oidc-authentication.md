---
title:  "Authenticating GitHub and Azure DevOps using OpenID Connect"
excerpt: "What if I told you, we could improve developer exprience **and** security with DevOps pipelines?  Enter OpenID Connect (OIDC) to the rescue!"
toc: true
tags:
  - Cloud
  - DevOps
  - Security
---

# Introduction

What if I told you, we could improve developer exprience **and** security with DevOps pipelines?  Enter OpenID Connect (OIDC) to the rescue!  Read on for more info!

## Traditional Secrets Management
Traditionally teams have used Entra ID (formerly Azure AD, RIP) service principals to allow Azure DevOps and GitHub Workflows to deploy resources using Azure Resource Manager to Azure.  This included setting up App Registrations, which are service principals with appropriate role-based access controls (RBAC).  This is still documented in Microsoft's documentation [here](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#create-an-azure-resource-manager-service-connection-that-uses-a-service-principal-secret). 

The problem with this approach is that now we are creating a client secret, basically a password, which has to be stored, managed and secured somewhere, just like any other password.  This could be Azure KeyVault or HashiCorp Vault.  This creates a security risk, in case this client secret (password) gets exposed.  This also presents a developer experience that is not ideal.

## A Better Approach with OIDC
A solution that is becoming increasingly popular involves using token exchange with OpenID Connect (OIDC). Microsoft even recommends this in their latest and greatest [documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect):

>However, using hardcoded secrets requires you to create credentials in the cloud provider and then duplicate them in GitHub as a secret. With OpenID Connect (OIDC), you can take a different approach by configuring your workflow to request a short-lived access token directly from the cloud provider. Your cloud provider also needs to support OIDC on their end, and you must configure a trust relationship that controls which workflows are able to request the access tokens. Providers that currently support OIDC include Amazon Web Services, Azure, Google Cloud Platform, and HashiCorp Vault, among others.

A step-by-step guide to configure OIDC using Workload Identity Federation can be found [here](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#create-an-azure-resource-manager-service-connection-that-uses-workload-identity-federation).

# Azure DevOps, Entra ID OIDC Authentication
A picture is worth a thousand words.  I've laid out the authentication flow in the diagram below for Azue DevOps.  No more secrets management!

## Azure DevOps, Entra ID, Azure Flow
<figure>
    <a href="/assets/images/azure-devops-oidc.jpg"><img src="/assets/images/azure-devops-oidc.jpg"></a>
</figure>

## Microsoft's Chief Architect John Savill Explains
Microsoft's Chief Architect John Savill explains OIDC authentication with Workload Identity Federation in his awesome YouTube videos that I have linked below.  

<iframe width="560" height="315" src="https://www.youtube.com/embed/saTUeR_U3lA?si=r3OMGYwt5DVX3Iii" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br/><br/>

# GitHub, Entra ID OIDC Authentication
How does this flow look for GitHub and GitHub Actions Workflows?  Well pretty similar to Azure DevOps and Azure Pipelines.  Check it out, gone are secrets!

## GitHub, Entra ID, Azure Flow
<figure>
    <a href="/assets/images/github-oidc.jpg"><img src="/assets/images/github-oidc.jpg"></a>
</figure>

## Microsoft's Chief Architect John Savill Explains
Once again, also check out Microsoft's Chief Architect John Savill explain this OIDC flow for GitHub.

<iframe width="560" height="315" src="https://www.youtube.com/embed/XkhkkLBkAT4?si=faDeaiViVhgPyBxA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
<br/><br/>
