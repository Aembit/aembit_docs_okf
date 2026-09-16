---
type: reference
title: "AI Assistant Integration Resources"
description: "Machine-readable documentation resources for AI coding assistants and LLMs integrating with Aembit."
resource: https://docs.aembit.io/llm-resources/
timestamp: 2026-09-15T18:18:13-07:00
---

# AI Assistant Integration Resources

Aembit publishes its documentation in two machine-readable formats for AI coding assistants and large language models (LLMs). The [Open Knowledge Format](https://openknowledgeformat.org) (OKF) bundle is a file tree your agent clones and navigates with file tools. The [llms.txt](https://llmstxt.org/) files are flat text you feed directly into a model’s context. Both cover the same material: workload identity concepts, Client Workload configuration, Access Policies, Trust Providers, and Credential Providers.

> **This page vs. the MCP Server**
>
> This page provides **machine-readable documentation** that you feed to AI coding assistants (Claude, Copilot, ChatGPT). It helps them generate code that integrates with Aembit.
>
> The [Aembit MCP Server](user-guide/mcp-server/overview.md) documentation covers **Aembit’s MCP integrations**—tools that let AI assistants directly query Aembit event logs, authorization events, and audit data.

> **Looking for human-readable docs?**
>
> This page provides machine-readable resources for AI coding assistants.
>
> **For human-readable docs:**
>
> * [Get Started Guide](get-started/overview.md) - understand Aembit workload identity concepts
> * [How Aembit Works](get-started/how-aembit-works.md) - understand Aembit’s architecture and security model
> * [Client Workload Configuration](user-guide/access-policies/client-workloads/overview.md) - configure workloads including Kubernetes integration
> * [User Guide](user-guide/overview.md) - configure and manage Aembit

## Choosing a format: OKF or llms.txt

Both formats carry the same documentation. Pick the one that matches how your AI tool consumes context.

* **Use the OKF bundle** when your agent has a persistent workspace and file tools (Claude Code, Cursor, Copilot agents). It clones the bundle once and reads only the files each task needs, so it never loads a guide-sized blob into context.
* **Use llms.txt** for one-shot context stuffing—a chat window or API call where you paste documentation directly into the prompt and have no filesystem to navigate.

When in doubt, prefer the OKF bundle for coding agents and llms.txt for chat assistants.

## The OKF documentation bundle

The OKF bundle is Aembit’s full documentation as a tree of small, cross-linked Markdown files. Each file carries `type`, `interface`, and `tags` frontmatter. An agent clones the bundle into your project and navigates it with file tools rather than loading the whole corpus into context. It reads a folder’s `index.md`, filters the entries by frontmatter, then follows the links it needs.

* **Repository:** [github.com/Aembit/aembit\_docs\_okf](https://github.com/Aembit/aembit_docs_okf) (force-synced on every documentation release)
* **Tarball:** [docs.aembit.io/okf.tar.gz](https://docs.aembit.io/okf.tar.gz)

### Point your agent at the bundle

Add this snippet to your project’s `CLAUDE.md` or `AGENTS.md` so your AI assistant knows the bundle exists and how to use it:

```markdown
## Aembit documentation (OKF bundle)


When configuring, integrating with, or troubleshooting Aembit, consult the local
Aembit documentation bundle rather than guessing or searching the web.


First-time setup (run once):


    git clone https://github.com/Aembit/aembit_docs_okf aembit-docs
    # no git? download and extract the tarball instead:
    # curl -fsSL "https://docs.aembit.io/okf.tar.gz" | tar -xz


To answer an Aembit question:


1. Read `aembit-docs/index.md` first — it explains how to navigate the bundle.
2. Open the relevant folder's `index.md`, then filter its files by the `type`,
   `interface`, and `tags` frontmatter to find the ones that fit the task.
3. Read only those two or three concept files, and follow their `## Related`
   links to reach counterparts in other guides.


Keep it current with `git -C aembit-docs pull`.
```

## Using these resources with AI assistants

Feed these resources to AI coding assistants (Claude, GitHub Copilot, ChatGPT) when you need help implementing Aembit integrations.

**Example workflows:**

* **Generating API integration code:** Provide `llms-api-cloud-endpoints.txt` or `llms-api-edge-endpoints.txt` when asking your LLM to generate API client code
* **Understanding configuration options:** Use `llms-full.txt` when asking questions about Aembit features, concepts, and configuration patterns
* **Troubleshooting:** Provide relevant resource to your LLM along with error messages for context-aware debugging suggestions

## Main documentation

Core Aembit documentation including [Get Started Guide](get-started/overview.md), [User Guide](user-guide/overview.md), [CLI Guide](dev-guide/cli/overview.md), and [support information](support-overview.md). All variants cover workload identity concepts, Client Workload configuration, Access Policies, Trust Providers, and Credential Providers.

* [llms.txt](https://docs.aembit.io/llms.txt) - Index of every page, each linking to that page’s Markdown (smallest download, load only the pages you need)
* [llms-full.txt](https://docs.aembit.io/llms-full.txt) - Complete main documentation including advanced configuration and examples (comprehensive)

## Aembit Cloud API

Complete API reference for the Aembit Cloud API, separated into focused resources for efficient token usage. Covers API endpoints, authentication, access management, policy configuration, and credential provider operations.

* [llms-api-cloud-full.txt](https://docs.aembit.io/llms-api-cloud-full.txt) - Complete Cloud API reference including endpoints and schemas (comprehensive)
* [llms-api-cloud-endpoints.txt](https://docs.aembit.io/llms-api-cloud-endpoints.txt) - Cloud API endpoints reference only (optimized for code generation)
* [llms-api-cloud-schemas.txt](https://docs.aembit.io/llms-api-cloud-schemas.txt) - Cloud API schemas reference only (data models and types)

## Aembit Edge API

Complete API reference for the Aembit Edge API, separated into focused resources for efficient token usage. Covers Edge deployment, Kubernetes integration, local authentication, and workload identity verification.

* [llms-api-edge-full.txt](https://docs.aembit.io/llms-api-edge-full.txt) - Complete Edge API reference including endpoints and schemas (comprehensive)
* [llms-api-edge-endpoints.txt](https://docs.aembit.io/llms-api-edge-endpoints.txt) - Edge API endpoints reference only (optimized for code generation)
* [llms-api-edge-schemas.txt](https://docs.aembit.io/llms-api-edge-schemas.txt) - Edge API schemas reference only (data models and types)
