---
title:  "Platform Agnostic Web App"
tags:
  - cloud
  - code
  - architecture
---

# Platform-Agnostic Web App
The general objective of this project is to demonstrate a cloud-agnostic, 12-factor web application that is deployed into a cloud environment for business continuity, agility, scalability and cost efficiency.  This flexibility will demonstrate the ability to avoid Cloud Service Provider (CSP) lock-in.
The primary objective is to demonstrate avoiding vendor lock-in while using PaaS.  Though similar principles apply to XaaS. 

# Detailed Description
The purpose of this project is to build a modular, cloud-agnostic, 12-factor design web application that focuses on business continuity and agility.  Businesses today strive to avoid Cloud Service Provider (CSP) vendor lock-in. Businesses must be agile and must ensure availability through business continuity best practices. As a result, this application will also be hosted in the Cloud, Microsoft Azure and Google Cloud Platform (GCP) for this project.  This modern web application is built using a modern cross-platform programming language, in this case ASP.NET 7 MVC, which can run on any system - Linux based or Windows based.  This application will be modular and consist of two parts – a database back-end and MVC (Model-View-Controller) front-end layer.  The actual contents of this application are less important, and in this case it's just a fictituous shopping application for demonstration purposes, because the aim is to build a web app that can be deployed to and hosted in any CSP, such as Microsoft Azure, Google Cloud Platform, or AWS or on any other platform, such as Tanzu or Kubernetes, at will to avoid vendor lock-in for maximum business agility.

### Logical Architecture using MVC Pattern
![Logical Architecture](https://github.com/TripleA-B/friendly-octo-giggle/blob/main/Logical%20Architecture.png)

### CI/CD
![CI/CD](https://github.com/TripleA-B/friendly-octo-giggle/blob/main/CI%20CD.png)

### Microsoft Azure Physical Architecture
![Microsoft Azure Physical Architecture](https://github.com/TripleA-B/friendly-octo-giggle/blob/main/Azure%20Physical%20Architecture.png)

### Google Cloud Platform Architecture
![Google Cloud Platform Architecture](https://github.com/TripleA-B/friendly-octo-giggle/blob/main/GCP%20Physical%20Architecture.png)
