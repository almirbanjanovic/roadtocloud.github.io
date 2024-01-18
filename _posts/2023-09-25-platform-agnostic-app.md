---
title:  "Platform Agnostic Web App"
layout: posts
author_profile: true
tags:
  - cloud
  - code
  - architecture
---

One of the core business tenets is business agility - the ability to adapt to changing market conditions. Similarly, businesses must be agile and must ensure availability through business continuity best practices.  Business either change or die.  How does this affect cloud in particular?  Avoiding Cloud Service Provider (CSP) vendor lock-in ensures this flexibility. 

In general, a modular, cloud-agnostic, application that follows the 12 factor methodology can provide business agility, continuity, scalability and cost efficiency.  Below I demonstrate avoiding vendor lock-in while using PaaS.  Though similar principles apply to XaaS (read "any"-as-a-Service). 

# Let's Dive Deeper
As already mentioned, 12 factor methodolgy can enable us to shift focuse between any application platform or CSP.  A modern web application is built using a modern cross-platform programming language, such as ASP.NET 7 MVC, which can run on any system - Linux based or Windows based.  One can either take a code-first or database-first approach.  This choice will largely depend on the size of the application and enterprise or team complexity - smaller teams may benefit from code-first, but larger teams often must follow strict change management processes, which require a database-first approach.  The below diagrams show a systematic approach to design such a modern web-app.  More detail and sample code can be found in my GitHub repo found here: "https://github.com/rimlaban7/platform-agnostic-app". 

### Logical Architecture using MVC Pattern
![Logical Architecture](assets/images/platform-agnostic-mvc-architecture.png)

### CI/CD
![CI/CD](https://github.com/RoadToCloud/roadtocloud.github.io/blob/main/assets/images/platform-agnostic-ci-cd-flow.png)

### Microsoft Azure Physical Architecture
![Microsoft Azure Physical Architecture](https://github.com/RoadToCloud/roadtocloud.github.io/blob/main/assets/images/platform-agnostic-azure-architecture.png)

### Google Cloud Platform Architecture
![Google Cloud Platform Architecture](https://github.com/RoadToCloud/roadtocloud.github.io/blob/main/assets/images/platform-agnostic-gcp-architecture.png)
