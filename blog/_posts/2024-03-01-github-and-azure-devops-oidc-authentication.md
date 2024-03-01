---
title:  "Authenticating GitHub and Azure DevOps using OpenID Connect"
header:
  #teaser: "/assets/images/github_terraform_azure_function.jpg"
excerpt: "What if I told you, that you could improve developer exprience **and** security with your DevOps pipelines?  Enter OpenID Connect (OIDC) to the rescue!"
toc: true
tags:
  - cloud
  - code
  - devops
---

# Introduction

Traditionally teams have used Entra ID (formerly Azure AD, RIP) service principals to allow Azure DevOps and GitHub Workflows to deploy resources using Azure Resource Manager to Azure.  This included setting up App Registrations, which are service principals with appropriate role-based access controls (RBAC).  This is still documented in Microsoft's documentation [here](https://learn.microsoft.com/en-us/azure/devops/pipelines/library/connect-to-azure?view=azure-devops#create-an-azure-resource-manager-service-connection-that-uses-a-service-principal-secret). 

The problem with this approach is that now we are creating a client secret, basically a password, which has to be stored, managed and secured somewhere, just like any other password.  This could be Azure KeyVault or HashiCorp Vault.  This creates a security risk, in case this client secret (password) gets exposed.  This also presents a developer experience that is not ideal.

A solution that is becoming increasingly popular involves using token exchange with OpenID Connect (OIDC). Microsoft even recommends this in their latest and greatest [documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect):

>However, using hardcoded secrets requires you to create credentials in the cloud provider and then duplicate them in GitHub as a secret. With OpenID Connect (OIDC), you can take a different approach by configuring your workflow to request a short-lived access token directly from the cloud provider. Your cloud provider also needs to support OIDC on their end, and you must configure a trust relationship that controls which workflows are able to request the access tokens. Providers that currently support OIDC include Amazon Web Services, Azure, Google Cloud Platform, and HashiCorp Vault, among others.
