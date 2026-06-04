---
title: "12-Factor Apps, Revisited for AI Agents"
date: 2026-06-04
header:
  teaser: "https://raw.githubusercontent.com/kaito-project/kaito/main/website/static/img/arch.png"
excerpt: "The 2011 Twelve-Factor App playbook still holds up for AI agents on Microsoft Foundry.  App now stands for agent."
toc: true
categories:
  - blog
tags:
  - ai
  - agents
  - foundry
  - cloud-native
  - 12-factor
---

I spent last few days watching [*Microsoft Build*](https://build.microsoft.com/en-US/home) sessions, mostly the *Microsoft Foundry* and agent-related ones — *Foundry Agent Service*, *Prompt Agents*, *Hosted Agents*, and the *Microsoft Agent Framework (MAF)*. The runtimes are new, the SDKs are new, the demos are impressive, and the whole time I kept thinking about a document written in 2011 for the Heroku and subsequent cloud-native era: the [Twelve-Factor App](https://12factor.net/).

We are about to make all the same mistakes again, just with agents instead of monoliths. The good news is the playbook to avoid those mistakes already exists, and *Twelve-Factor* is most of it.

What I would like to do here is take the *original* twelve, exactly as they were written for cloud-native web apps, and walk through what each one means now that the "app" is an agent — and what Microsoft, Microsoft Foundry and the broader Azure ecosystem give you to actually follow them.

Now before we get to the good stuff, *Twelve-Factor* is necessary but it is not really entirely sufficient for agents. Some factors map cleanly, some need a bit of stretching, a couple are kind of weak fits, and several of the things that will get you paged at 3 a.m. on an agent in production are not in the original twelve at all. We'll talk about those at the end.

## The Big Picture

| # | Factor | Fit | Foundry / Azure enabler |
|---|---|---|---|
| I | Codebase | Strong | GitHub, GitHub Actions, Bicep, Foundry SDK and REST |
| II | Dependencies | Strong | Pinned model deployments, image digests in *Azure Container Registry (ACR)*, pinned SDKs |
| III | Config | Strong | *Azure App Configuration*, *Azure Key Vault*, agent versioning, *Entra* managed identity |
| IV | Backing services | Very strong | *Azure AI Search*, *Foundry IQ*, *Cosmos DB*, *Blob Storage*, *MCP* servers, *Functions* |
| V | Build, release, run | Strong | Actions to *ACR* to *Hosted Agents*, agent versioning, publish as *Agent Application* |
| VI | Processes | Reinterpret | Stateless *Hosted Agents* containers, thread state in Foundry or BYO *Cosmos* |
| VII | Port binding | Weak | The *Responses API* is the entry point; the platform owns the surface |
| VIII | Concurrency | Reinterpret | Quota and TPM management, *Functions* Flex Consumption, *Service Bus* for async |
| IX | Disposability | Mixed | Fast container startup, checkpointing and restartability in *MAF*, run cancellation, idempotent tools |
| X | Dev/prod parity | Strong but hard | Foundry environments, IaC, eval datasets, per-environment model pinning |
| XI | Logs | Needs expansion | *OpenTelemetry*, *Application Insights*, Foundry tracing, continuous evaluation |
| XII | Admin processes | Moderate | *Functions* and *Container Apps Jobs*, batch eval, dataset curation, fine-tuning |

In the rest of this post I'll focus on the factors that I think need the most discussion. The ones I do not call out below are either obvious or covered well by the table above.

## Codebase

One of the most common architecture question I get right now is some variation of "do we put all our agents in one repo?" Well, my answer is usually "no". One agent, one repo — at least when the agent is its own deployable and lifecycle-managed unit. Same conversation we have been having about microservices for over a decade, just with new building blocks. The agent's instructions, its tool schemas (the model-facing invocation contracts), its model bindings, its eval datasets sit together and version together. Dev, test, and prod are deploys of the same codebase.  

Reusable tools and shared skills are a different question. Those belong in their own repos — an MCP server, an Azure Functions tool app, a shared prompt library — published, versioned, and referenced by the agent repo that depends on them. Same logic that has guided shared library design for around fifteen years. There is nothing new or exotic about this.  

And here is where I go into a tangential mandatory rant about ClickOps. The Foundry portal is excellent for authoring and debugging, but it should not be your default unless you're building simple prompt agents for demos or PoCs. Define your agents declaratively through the Foundry SDK or REST API, store those definitions in Azure DevOps or GitHub, deploy them with a pipeline, and provision the surrounding infrastructure with Bicep or Terraform. Treat the portal as a viewer.  Let's make sure we use DevOps best practices that have been guiding us for years.  Stay away from ClickOps.


## Backing Services

The Backing services factor could very well be the cleanest 1:1 mapping in the whole list, and it is also the factor that Foundry was clearly designed around. Every meaningful capability and piece of state the agent depends on is an attached resource. 

For example, semantic retrieval comes from [*Azure AI Search*](https://learn.microsoft.com/en-us/azure/search/search-what-is-azure-search?tabs=indexing%2Cquickstarts) or [*Foundry IQ*](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/what-is-foundry-iq?tabs=portal). Conversation and thread state is Foundry-managed or, when data residency matters, your own [*Cosmos DB*](https://learn.microsoft.com/en-us/azure/cosmos-db/overview). Files and artifacts go in [*Blob Storage*](https://learn.microsoft.com/en-us/azure/storage/blobs/storage-blobs-overview). Tools can be managed through the new [Foundry Toolbox](https://learn.microsoft.com/en-us/azure/foundry/agents/how-to/tools/toolbox?pivots=dotnet) or can exist as [*Azure Functions* apps](https://learn.microsoft.com/en-us/azure/azure-functions/scenario-custom-remote-mcp-server?pivots=programming-language-csharp), [*Logic Apps* workflows](https://learn.microsoft.com/en-us/azure/logic-apps/create-model-context-protocol-server-standard), or custom *MCP* servers. Secrets live in [*Azure Key Vault*](https://learn.microsoft.com/en-us/azure/key-vault/general/overview). [*Microsoft Entra*](https://learn.microsoft.com/en-us/entra/fundamentals/what-is-entra) handles identity and Microsoft Entra Agent ID gives Foundry agents governable identities. Every one of these is swappable by changing configuration, not by changing code.

The discipline is the same one we have always had to enforce — do not hard-code an index name into a system prompt, and do not bake a tool endpoint into a container. The Foundry tools abstraction makes this easier because file search, web search, code interpreter, *MCP*, and your own custom functions all show up through a uniform interface. The platform pushes you toward the right shape if you let it.  Check out this official blog post [Introducing Toolboxes in Foundry](https://devblogs.microsoft.com/foundry/introducing-toolboxes-in-foundry/) that clearly aims to implement DRY (don't repeat yourself) principles and reusability.

## Processes

A strict reading of *Processes* says applications should be stateless and share-nothing. This is one of those places where the *system* an agent participates in is easy to confuse with the *agent itself*. The agent — the code that runs the loop, calls the model, invokes tools, and returns a response — **should absolutely be stateless**. The conversation history, retrieved context, memory, tool outputs, and approval queues that the agent reasons over are state, but that state does not belong inside the agent process. It belongs in attached, observable, replaceable backing services that we discussed above. Pretending you can keep that state in process memory will get you in trouble fast.

The principle that survives, and actually gets more important with agents, is this: the *compute* that hosts the agent stays stateless, and every piece of state lives in a backing service you can see, swap, and back up. Don't keep conversation history memory, don't cache embeddings on the container's local disk, and don't treat the model's context window as durable memory — push all of it to an attached resource. [*Hosted Agents*](https://learn.microsoft.com/en-us/azure/foundry/agents/concepts/hosted-agents) in Foundry split this cleanly — the container itself is disposable, but per-session state (filesystem under $HOME, files, conversation) is persisted by the platform, not by your process. Session state, threads, files, and vectors are persisted in Foundry-managed storage or in your own *Cosmos DB*, *Blob Storage*, and *AI Search*. 

## Logs

Treat logs as event streams. That advice is still correct, and it is still not enough for an agent. For a traditional web app, stdout plus a log aggregator gets you most of the way home. For an agent, you also need traces of every prompt and completion, traces of every tool call (with inputs, outputs, retries, and partial failures), retrieval diagnostics including the documents grounding each answer, token and cost telemetry, model and prompt versions on every span, safety and content-filter events, and the eval results that gate each release.

On Azure this is *OpenTelemetry* into *Azure Application Insights* and *Azure Monitor*, with Foundry's built-in tracing already emitting agent-shaped spans. *Continuous evaluation* in Foundry feeds back into that observability story by treating eval scores as first-class telemetry, not as something you only look at during development. The way I would phrase the updated factor: logs are necessary, traces are required, and evals are part of your observability surface.

## What Is *Not* in the Original Twelve

*Twelve-Factor* was written for stateless web apps without LLMs, and it does not address failure modes that will most likely take down an agent. A short list of what the original twelve do not cover, with the Microsoft-side answer next to each:

1. **Prompt injection and tool-call authorization** — assume every retrieved document and every user message is hostile. *Azure AI Content Safety* and Foundry's policy controls do the input/output filtering.
2. **Evaluation as a release gate** — agents need an eval suite the same way services need unit tests. Foundry's evaluation tooling and continuous evaluation pipelines plug into *GitHub Actions* so you can wire up a gate that blocks promotion when an agent version regresses on a known dataset.
3. **Model rollback and drift** — pin model deployments, keep the previous version warm, and have a documented rollback path. The Foundry model catalog and per-environment model bindings make this straightforward, but only if you actually use them.
4. **Human-in-the-loop** — for any action with real-world side effects, design the approval step into the agent from day one. *Logic Apps*, *Teams* adaptive cards, and *Power Automate* approvals are all production-ready integrations for this.
5. **Multi-agent coordination and handoff state** — once you have more than one agent, the state that lives *between* them becomes its own first-class concern. *MAF* gives you primitives for orchestration, but the discipline of treating handoff state as an explicit, observable, replayable artifact is on you.

## Where Does this Leave Us?

We're in the AI era now and things move fast. However, I've been noticing we are repeating the same mistakes we fought hard to avoid with cloud-native apps.  The point of this post is not that *Twelve-Factor* is a perfect lens for agents. It may or may not be. The point is that the discipline behind it — declarative codebases, pinned dependencies, externalized config, attached backing services, immutable builds, observable state, real observability — is the discipline we followed with cloud-native apps for years and it is exactly the discipline we need right now, when the temptation to glue together prompt-and-tool prototypes and call them production systems is at an all-time high.

Microsoft Foundry gives you the runtime and most of the building blocks. Azure gives you the rest. The original twelve give you the operating model. The new failure modes give you the gaps to fill on top.

Build the boring parts well. Sleep well at night. Your agent will thank you for it.
