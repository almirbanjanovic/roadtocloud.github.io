---
title: "12-Factor Apps, Revisited for AI Agents"
date: 2026-06-04
categories:
  - blog
tags:
  - ai
  - agents
  - foundry
  - cloud-native
  - 12-factor
---

I spent last week watching *Microsoft Build* sessions, mostly the *Microsoft Foundry* and agent-related ones — *Foundry Agent Service*, *Prompt Agents*, *Hosted Agents*, and the *Microsoft Agent Framework (MAF)*. The runtimes are new, the SDKs are new, the demos are impressive, and the whole time I kept thinking about a document written in 2011 for the Heroku era: the [Twelve-Factor App](https://12factor.net/).

We are about to make all the same mistakes again, just with agents instead of monoliths. The good news is the playbook to avoid those mistakes already exists, and *Twelve-Factor* is most of it.

Before going any further — Dex Horthy has already written [12-Factor Agents](https://github.com/humanlayer/12-factor-agents), which is an agent-native rewrite of the original twelve. It is a great read and I would not try to replace it. What I want to do here is different. I want to take the *original* twelve, exactly as they were written for cloud-native web apps, and walk through what each one means now that the "app" is an agent — and what Microsoft Foundry and the broader Azure ecosystem give you to actually follow them.

A quick disclaimer before the long table. *Twelve-Factor* is necessary but it is not sufficient for agents. Some factors map cleanly, some need a bit of stretching, a couple are honestly weak fits, and several of the things that will get you paged at 3 a.m. on an agent in production are not in the original twelve at all. I will get to those at the end.

## The Big Picture

| # | Factor | Fit | Foundry / Azure enabler |
|---|---|---|---|
| I | Codebase | Strong | GitHub, GitHub Actions, Bicep, Foundry SDK and REST |
| II | Dependencies | Strong | Pinned model deployments, image digests in *Azure Container Registry (ACR)*, pinned SDKs |
| III | Config | Strong | *Azure App Configuration*, *Azure Key Vault*, agent versioning, *Entra* managed identity |
| IV | Backing services | Very strong | *Azure AI Search*, *Foundry IQ*, *Cosmos DB*, *Blob Storage*, *MCP* servers, *Functions* |
| V | Build, release, run | Strong | Actions to *ACR* to *Hosted Agents*, agent versioning, *Entra Agent Registry* |
| VI | Processes | Reinterpret | Stateless *Hosted Agents* containers, thread state in Foundry or BYO *Cosmos* |
| VII | Port binding | Weak | The *Responses API* is the entry point; the platform owns the surface |
| VIII | Concurrency | Reinterpret | Quota and TPM management, *Functions* Flex Consumption, *Service Bus* for async |
| IX | Disposability | Mixed | Fast container startup, graceful shutdown in *MAF*, run cancellation, idempotent tools |
| X | Dev/prod parity | Strong but hard | Foundry environments, IaC, eval datasets, per-environment model pinning |
| XI | Logs | Needs expansion | *OpenTelemetry*, *Application Insights*, Foundry tracing, continuous evaluation |
| XII | Admin processes | Moderate | *Functions* and *Container Apps Jobs*, batch eval, dataset curation, fine-tuning |

The rest of this post zooms in on the factors that I think need the most discussion. The ones I do not call out below are either obvious or covered well by the table above.

## Codebase — Yes, One Repo Per Agent

The most common architecture question I get right now is some variation of *"do we put all our agents in one repo?"* My answer is no. One agent, one repo. Same conversation we have been having about microservices for a decade, just with new building blocks. The agent's instructions, its tool schemas, its model bindings, its eval datasets, and its infrastructure-as-code all sit together and version together. Dev, test, and prod are deploys of the same codebase.

Reusable tools and shared skills are a different question, and one I get asked about almost as often. Those belong in *their own* repos — an *MCP* server, an *Azure Functions* tool app, a shared prompt library — published, versioned, and referenced by the agent repo that depends on them. Same logic that has guided shared library design for fifteen years. Nothing exotic about it.

A note on Foundry specifically. The Foundry portal is excellent for authoring and debugging, but it should not be your system of record. Define your agents declaratively through the Foundry SDK or REST API, store those definitions in GitHub, deploy them with *GitHub Actions*, and provision the surrounding infrastructure with *Bicep* or *Terraform*. Treat the portal as a viewer.

## Backing Services — Where Foundry Really Pays Off

The *Backing services* factor is the cleanest 1:1 mapping in the whole list, and it is also the factor that Foundry was clearly designed around. Every meaningful piece of an agent is an attached resource.

Retrieval is *Azure AI Search* or *Foundry IQ*. Conversation and thread state is Foundry-managed or, when data residency matters, your own *Cosmos DB*. Files and artifacts go in *Blob Storage*. Tools are *Azure Functions* apps, *Logic Apps* workflows, or custom *MCP* servers. Secrets live in *Azure Key Vault*. Identity is *Microsoft Entra*. Every one of these is swappable by changing configuration, not by changing code.

The discipline is the same one we have always had to enforce — do not hard-code an index name into a system prompt, and do not bake a tool endpoint into a container. The Foundry tools abstraction makes this easier because file search, web search, code interpreter, *MCP*, and your own custom functions all show up through a uniform interface. The platform pushes you toward the right shape if you let it.

## Processes — The Goal Is Not "No State", It Is No *Hidden* State

A strict reading of *Processes* says applications should be stateless and share-nothing. Agents are not stateless. They have conversations, retrieved context, memory, tool outputs, and approval queues. Pretending otherwise will get you in trouble fast.

The principle that survives, and actually gets more important with agents, is this: the *compute* should be stateless, and every piece of state should live in an attached, observable, replaceable backing service. *Hosted Agents* in Foundry are a clean example. The container is ephemeral. Session state, threads, files, and vectors are persisted in Foundry-managed storage or in your own *Cosmos DB*, *Blob Storage*, and *AI Search*. The rule, restated for the agent era: no hidden, ungoverned state anywhere in the system.

## Port Binding and Concurrency — Where Foundry Inverts the Original

*Port binding* is the factor that fits agents worst, because Foundry inverts the original assumption. In *Twelve-Factor*, the app exports its own service by binding to a port. With Foundry, the platform owns the surface. Your agent is reached through the *Responses API*, and the entry point is the same whether the agent is a portal-authored *Prompt Agent*, a custom container in *Hosted Agents*, or your own process calling the *Responses API* from somewhere else entirely. The honest way to read this factor in 2026 is that the agent should have a clean invocation contract — through the *Responses API* or via *MCP* if it is a tool — and let the platform handle the ports.

*Concurrency* needs a similar reread. The original factor is about scaling out via the Unix-style process model. With Foundry, you are not provisioning replicas the way you would for a stateless web app. Concurrency for an agent is bounded by model quota and tokens-per-minute, by tool latency, by downstream system limits, and by how well your async paths are wired up. The Azure-side answer is a combination of *TPM* management on your model deployments, *Azure Functions* on Flex Consumption for scale-to-zero tools, and *Service Bus* or *Event Grid* when you need to fan out work asynchronously. The mental model shifts from "more processes" to "more headroom across the whole pipeline."

## Logs — One Factor That Has Outgrown Itself

Treat logs as event streams. That advice is still correct, and it is still not enough for an agent. For a traditional web app, stdout plus a log aggregator gets you most of the way home. For an agent, you also need traces of every prompt and completion, traces of every tool call (with inputs, outputs, retries, and partial failures), retrieval diagnostics including the documents grounding each answer, token and cost telemetry, model and prompt versions on every span, safety and content-filter events, and the eval results that gate each release.

On Azure this is *OpenTelemetry* into *Azure Application Insights* and *Azure Monitor*, with Foundry's built-in tracing already emitting agent-shaped spans. *Continuous evaluation* in Foundry feeds back into that observability story by treating eval scores as first-class telemetry, not as something you only look at during development. The way I would phrase the updated factor: logs are necessary, traces are required, and evals are part of your observability surface.

## What Is *Not* in the Original Twelve

This is the part that matters most. *Twelve-Factor* was written for stateless web apps without LLMs, and it does not address the failure modes that will most likely take down an agent. A short list of what the original twelve do not cover, with the Microsoft-side answer next to each:

1. **Prompt injection and tool-call authorization** — assume every retrieved document and every user message is hostile. *Microsoft Entra* gives each agent its own identity; tool calls authorize the agent, not the user behind it. *Azure AI Content Safety* and Foundry's policy controls do the input/output filtering.
2. **Evaluation as a release gate** — agents need an eval suite the same way services need unit tests. Foundry's evaluation tooling and continuous evaluation pipelines plug directly into *GitHub Actions* so that an agent version cannot be promoted if it regresses on a known dataset.
3. **Model rollback and drift** — pin model deployments, keep the previous version warm, and have a documented rollback path. The Foundry model catalog and per-environment model bindings make this straightforward, but only if you actually use them.
4. **Human-in-the-loop** — for any action with real-world side effects, design the approval step into the agent from day one. *Logic Apps*, *Teams* adaptive cards, and *Power Automate* approvals are all production-ready integrations for this.
5. **Multi-agent coordination and handoff state** — once you have more than one agent, the state that lives *between* them becomes its own first-class concern. *MAF* gives you primitives for orchestration, but the discipline of treating handoff state as an explicit, observable, replayable artifact is on you.

These are not extensions to *Twelve-Factor*. They are the gaps it leaves, and they are the gaps that will determine whether your agent is something you can confidently put in front of a customer.

## Where That Leaves Us

The point of this post is not that *Twelve-Factor* is a perfect lens for agents. It is not. The point is that the discipline behind it — declarative codebases, pinned dependencies, externalized config, attached backing services, immutable builds, observable state, real observability — is exactly the discipline we need right now, when the temptation to glue together prompt-and-tool prototypes and call them production systems is at an all-time high.

Microsoft Foundry gives you the runtime and most of the building blocks. Azure gives you the rest. The original twelve give you the operating model. The new failure modes give you the gaps to fill on top.

Build the boring parts well. The agent will thank you for it.
