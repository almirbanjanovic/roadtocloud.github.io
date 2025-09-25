---
title: "Modular Cloud Playground with Infra-as-Code (Terraform + Bicep)"
header:
  teaser: "/assets/images/cloud-playground-infra.png"
excerpt: "A modular Azure playground repo with ready-to-run GitHub Actions for both Terraform and Bicep."
toc: true
tags:
  - Cloud
  - Azure
  - Terraform
  - Bicep
  - DevOps
  - GitHub Actions
---

# Cloud Playground with Infra-as-Code

One of the things I always tell people who are learning cloud is this: you need a safe space to experiment. Somewhere you can break stuff, try new patterns, rebuild quickly, and not worry about production blowing up.

That’s why I built this: [**cloud-playground-infra**](https://github.com/almirbanjanovic/cloud-playground-infra).

---

## Why I Put This Together

I’ve been in plenty of conversations with peers and customers who want to “get hands-on” but aren’t sure how to do it without risk. My answer has always been: build a playground.

But instead of manually clicking around in the Azure portal, I wanted a repeatable, codified setup. Besides, clicking around the portal may be good conceptually but it is definitely not representative of real world scenarios. So I created something modular enough where I can spin up only the pieces I need, swap parts in and out, and still have a consistent foundation. And something that shows the differences between tools like **Terraform** and **Bicep** while making it dead simple to use either one in practice.

---

## What’s Inside

The repo is structured to be modular and language-agnostic.  Pick your favorite IaC language (Bicep or Terraform) and follow directions in the README within the [**cloud-playground-infra**](https://github.com/almirbanjanovic/cloud-playground-infra) repo.
 
- GitHub Actions workflows for both → This is where it gets powerful. The repo already includes two CI/CD pipelines:  
  - `terraform-deploy.yml` for Terraform deployments  
  - `bicep-deploy.yml` for Bicep deployments  

  You don’t need to wire up your own automation — just call the workflow for the language you prefer, and the pipeline handles the provisioning end to end.  To emphacise again, this assumes you've followed instructions in the README within the [**cloud-playground-infra**](https://github.com/almirbanjanovic/cloud-playground-infra) repo.

The idea is simple: cloud playgrounds. You can work in Terraform or Bicep, keep the repo modular, and plug into the corresponding pipeline. The workflows abstract the heavy lifting — you just choose your IaC language and run the pipeline.
