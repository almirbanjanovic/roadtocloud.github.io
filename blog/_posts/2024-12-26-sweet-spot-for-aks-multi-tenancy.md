---
title:  "The Sweet Spot for Azure Kubernetes Service Multi-Tenancy"
header:
  teaser: "/assets/images/kubernetes-namespace-multi-tenancy.png"
excerpt: "Explore the sweet spot for Azure Kubernetes Service multi-tenancy: balancing cost, reliability, and security using namespaces and robust policies."
#classes: wide
toc: true
tags:
  - Cloud
---

# Introduction

<figure style="width: 350px" class="align-left">
	<a href="/assets/images/kubernetes-namespace-multi-tenancy.png"><img src="/assets/images/kubernetes-namespace-multi-tenancy.png"></a>
	<figcaption>Created with DALL-E 3</figcaption>
</figure>

Azure Kubernetes Service (AKS) is a powerful tool for managing containerized applications at scale. However, as organizations adopt AKS, the question of multi-tenancy often arises: how do you design an architecture that balances reliability, cost, and security when serving multiple tenants? For smaller companies with limited funding, where cost-optimization is paramount, this question is particularly crucial. 

Having worked at a large Fortune 50 organization, where funding was not always the critical decider, I witnessed firsthand how cloud costs, initially seen as manageable, ballooned over time and became a significant issue. Cloud costs became such a significant issue, that they necessitated cost-cutting across IT, including layoffs (or RIFs as they are colloquially known). Now, working for a smaller organization, I’ve come to appreciate the "magic triangle" of cloud cost (directly related to funding), reliability and security. These three elements must work hand in hand to build a sustainable, scalable, secure and cost-conscious infrastructure. What does this mean in the context of Azure and Kubernetes?

The answer lies in identifying the "sweet spot" for multi-tenancy. While the [Azure Architecture Framework](https://learn.microsoft.com/en-us/azure/architecture/guide/multitenant/service/aks) outlines several approaches—ranging from fully isolated clusters per tenant to namespace-based separation—there is a middle ground that offers an effective compromise for startups and smaller organizations. This post explores that sweet spot: using namespaces as tenant separators to separate transactional workloads, combined with strict security measures to maintain isolation and control.

# Why Namespace-Based Multi-Tenancy?
Namespaces in Kubernetes provide logical separation within a cluster, making them an ideal candidate for multi-tenancy in cost-conscious environments. Here are a few reasons why namespace-based separation is appealing:

1. **Cost Efficiency:** Running separate clusters for each tenant can be prohibitively expensive. By sharing a single AKS cluster, you reduce the operational and financial overhead associated with maintaining multiple clusters.

2. **Resource Management:** Kubernetes namespaces allow you to apply resource quotas, limits, and policies at the namespace level. This enables you to allocate resources fairly and prevent tenants from over-consuming cluster resources.

3. **Scalability:** A namespace-based approach is well-suited to scaling tenant workloads within a single cluster, allowing smaller companies to start lean and expand gradually.

# Achieving Secure Namespace Separation
Security is a common concern when sharing a cluster across multiple tenants. To address this, it is critical to implement robust measures that prevent cross-tenant interference. Here’s how you can do it:

Kubernetes’ built-in network policies can enforce strict traffic isolation. For example, you can configure network policies to:
   - Block communication between namespaces.
   - Allow traffic only from trusted ingress controllers.
   - Restrict egress traffic to specific external endpoints.

To limit network traffic for pods within a namespace so that they can only communicate with other pods in the same namespace, you can define a Kubernetes `NetworkPolicy` as follows:

{% highlight yaml %}
{% raw %}
   apiVersion: networking.k8s.io/v1
   kind: NetworkPolicy
   metadata:
     name: <restrict-to-namespace>
     namespace: <your-namespace-name>
   spec:
     podSelector: {}
     policyTypes:
     - Ingress
     - Egress
     ingress:
     - from:
       - podSelector: {}
     egress:
     - to:
       - podSelector: {}
{% endraw %}
{% endhighlight %}

There you have it!  Pretty simple and straightforward.  We can use `NetworkPolicy` to balance security and optimize cost using a single Kubernetes cluster. For more information and more complex scenarios, take a look at the official Kubernetes [Network Policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) documentation.

# Separate Resource Groups

While namespaces handle separation within the cluster for transaction based processing through pods, other Azure resources that store data (such as Storage Accounts, Databases, Key Vaults, etc.) should reside in tenant-specific resource groups. Data needs to be strictly separated to maintain compliance and security.

# Final Thoughts
For startups and smaller companies, namespace-based multi-tenancy offers a pragmatic balance between cost and control. By leveraging namespaces for separation and implementing strict security measures, you can create a multi-tenant architecture that scales with your business. 
