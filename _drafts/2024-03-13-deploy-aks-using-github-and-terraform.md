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

# Overview

This post will demonstrate the deployment of an Azure Kubernetes Service using Terraform and GitHub Actions for CI/CD.  The full code can be found in my GitHub [azure-kubernetes-service](https://github.com/rimlaban7/azure-kubernetes-service-terraform) repository. For this project we'll also make sure GitHub and Azure use OpenID Connect (OIDC) for authenticating service principals. This will save us from storing client secrets (basically passwords).  For an overview of how this works, see my blog post titled [Authenticating GitHub and Azure DevOps using OpenID Connect ](https://www.theroadtocloud.com/blog/github-and-azure-devops-oidc-authentication/).

# Prerequisites

Before you begin, you'll need to have the following:

- An Azure subscription
- A GitHub account
- Your favorite IDE - I prefer Visual Studio Code with the below extensions installed
    - GitHub Actions
    - HashiCorp HCL 
    - HashiCorp Terraform

# Configuration

## OpenID Connect (OIDC)

We'll need to create an Entra application and service principal that has the appropriate permissions to create and modify Azure resources.  There are a few different ways to accomplish this depending on you subscription type and preferred method (Azure Portal, Azure CLI, or PowerShell).  For this project, I followed this guide: [Configuring OpenID Connect in Azure](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure).  For this to work correctly, you'll need to have the below values configured.

| Configuration Item | Value                                   |
| ------------------ | --------------------------------------- |
| Organization       | *GitHub User Name or Organization Name* |
| Repository         | *Repository Name*                       |
| Entity Type        | `Environment`                           |
| Based on selection | `prod`                                  |


## GitHub Actions Environment Secrets

The GitHub Actions and Terraform code we will use for this project require an environment as well as environment secrets to be configured. For step-by-step instructions on how to create an environment, see [Creating an Environment](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment#creating-an-environment). We'll need to call this environment *prod*, as stated above for the GitHub Actions to work correctly.  Add the below repository secrets.

- AZURE_TENANT_ID
- AZURE_SUBSCRIPTION_ID
- AZURE_CLIENT_ID
- ACCOUNT_REPLICATION_TYPE
- ACCOUNT_TIER
- AZ_CLI_LOCATION
- TF_LOCATION
- BLOB_CONTAINER_NAME
- ENVIRONMENT
- RESOURCE_GROUP_NAME
- STORAGE_ACCOUNT_PREFIX

Once both OIDC is configured in Azure, and these repository secreta above are added to GitHub environment secrets, you can run `test-oidc.yml` to validate whether or not your GitHub Actions Workflow can connect to Azure.


## Initialize Terraform Remote State Storage

When you execute `terraform init`, you're setting up the providers and linking to any existing state of the infrastructure. For this project, the backend state is kept in an Azure storage account.  You only need to run the below workflow once, and it will configure an Azure storage account for Terraform remote state.

<br>
{% highlight yaml %}
{% raw %}
name: Initialize Backend Terraform State Storage
on: 
  workflow_dispatch

permissions:
      id-token: write
      contents: read
      
jobs: 
  initialize-state-storage:
    runs-on: ubuntu-latest

    # Use the Bash shell regardless whether the GitHub Actions runner is ubuntu-latest, macos-latest, or windows-latest
    defaults:
        run:
          shell: bash

    steps:
        - uses: actions/checkout@v4

        - name: Azure login
          uses: azure/login@v2
          with:
            client-id: ${{ secrets.AZURE_CLIENT_ID }}
            tenant-id: ${{ secrets.AZURE_TENANT_ID }}
            subscription-id: ${{ secrets.AZURE_SUBSCRIPTION_ID }}

        # Execute Azure CLI script of a specific CLI version via file present in your repository.
        - name: Azure CLI script file
          uses: azure/cli@v2
          with:
            azcliversion: 2.30.0
            inlineScript: |
              az group create --name ${{ secrets.RESOURCE_GROUP_NAME }} --location ${{ secrets.REGION }} 
              az storage account create --resource-group ${{ secrets.RESOURCE_GROUP_NAME }} --name ${{ secrets.STORAGE_ACCOUNT_NAME }} --sku ${{ secrets.STORAGE_ACCOUNT_SKU }} --encryption-services blob
              az storage container create --name ${{ secrets.BLOB_CONTAINER_NAME }} --account-name ${{ secrets.STORAGE_ACCOUNT_NAME }}
{% endraw %}
{% endhighlight %}
<br>    
