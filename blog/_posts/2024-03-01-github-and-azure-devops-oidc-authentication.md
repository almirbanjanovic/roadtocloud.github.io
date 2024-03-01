---
title:  "Authenticating GitHub and Azure DevOps using OpenID Connect"
header:
  teaser: "/assets/images/github_terraform_azure_function.jpg"
excerpt: "What if I told you, that you could improve developer exprience **and** security with your DevOps pipelines?  Enter OpenID Connect (OIDC) to the rescue!"
toc: true
tags:
  - cloud
  - code
  - devops
---

# Introduction

Traditionally teams have used service principals to allow Azure DevOps and GirHub Workflows to deploy to Azure.  This included setting up App Registrations (service principals with appropriate RBAC rules) credentials in Entra ID (formerly Azure AD).  The one problem has been the generation of a client secret, basically a passeord, which has to be stored at the client.  This creates a security risk, in case this client secret (password) gets exposed.  This also presents a developer experience that is not ideal.
