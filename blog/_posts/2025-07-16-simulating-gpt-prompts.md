---
title:  "Simulating GPT Prompts in C# — A Console App with Real-World Intent"
#header:
  #teaser: "/assets/images/kubernetes-namespace-multi-tenancy.png"
excerpt: "This console app, built with .NET 9 in Visual Studio Code using GitHub Copilot Chat and Agent Mode, is just the beginning of a full-stack, AI-powered journey to AKS."
#classes: wide
toc: true
tags:
  - Cloud
---

# Simulating GPT Prompts in C# — A Console App with Real-World Intent

Every cloud-native solution starts with a foundation. In this case, it’s a C# console application — intentionally simple, but architecturally aligned with the broader goal: building a full-stack, AI-enabled assistant powered by Azure OpenAI and deployed to Azure Kubernetes Service (AKS).

This console app simulates GPT-style prompts and responses. But more importantly, it sets the tone for the rest of the system — clean architecture, async-first design, and modular components that scale from local dev to production-grade cloud.

🔗 [View the code on GitHub](https://github.com/almirbanjanovic/copilot-console-simulator/actions)

---

## Why Start with a Console App?

Before deploying to AKS or wiring up Azure OpenAI, I wanted to isolate the core logic: prompt handling, response simulation, and structured logging. This CLI tool provides a focused environment to build and test that logic — without distractions.

It’s not a prototype. It’s a foundational component.

---

## What It Does

The `copilot-console-simulator`:

- Accepts user input from the terminal
- Simulates a GPT-4 Turbo-style response
- Logs prompt/response pairs to an in-memory list or JSON file
- Uses interfaces and dependency injection for clean separation of concerns
- Is structured to plug directly into the backend API layer

---

## Built with the Right Tools

This project was developed entirely in **Visual Studio Code**, using:

- `.NET 9`
- `ILogger<T>` for structured logging
- `async/await` for non-blocking I/O
- Clean architecture patterns (interfaces, DI, separation of concerns)

To accelerate development and stay in flow, I used **GitHub Copilot Chat** and **Agent Mode**. This combination allowed me to:

- Scaffold services and interfaces quickly
- Ask contextual questions directly in the IDE
- Stay focused on architecture and flow, not boilerplate

---

## Strategic Fit

This app is the first of several purpose-built repositories that make up the full solution:

- `gpt-api-backend`: ASP.NET Core Web API that connects to Azure OpenAI and SQL Server
- `gpt-web-client`: Razor/Blazor frontend for chat interaction
- `gpt-db-schema`: SQL schema + EF Core scaffolding
- `iac-terraform` and `iac-bicep`: Infrastructure-as-code for AKS, ACR, Key Vault, and more

Each repo is modular, version-controlled, and aligned with cloud-native best practices.

---

# What’s Next

With the simulator complete, the next step is designing the database schema and scaffolding EF Core models. From there, I’ll move into the API layer, frontend, containerization, and infrastructure deployment.

This is a journey — and this console app is the first real checkpoint.

---

📁 Repo: [copilot-console-simulator](https://github.com/almirbanjanovic/copilot-console-simulator/actions)  
🧠 Built with: GitHub Copilot Chat + Agent Mode 
🛠️ Editor: Visual Studio Code  
🧱 Runtime: .NET 9

Let’s keep building.
