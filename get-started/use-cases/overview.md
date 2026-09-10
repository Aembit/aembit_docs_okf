---
type: reference
title: "Aembit use cases"
description: "Find the right starting point for your Aembit implementation"
resource: https://docs.aembit.io/get-started/use-cases/
tags: ["use-case"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit use cases

Aembit replaces static secrets with identity-based access. It secures two kinds of non-human identity, AI agents and workloads, so find your starting point below based on what you’re securing.

> **New to Aembit?**
>
> Start with **CI/CD Pipelines**. It’s the fastest path to seeing Aembit in action, with no agent deployment required.

## AI

Aembit secures AI access in both directions: AI agents reaching into your systems (directly or through MCP servers), and your own applications reaching out to LLM APIs. All three use cases share the same identity-first model.

[Securing AI agent access to your resources](ai-agents.md)Secure Claude Desktop, Gemini CLI, and other MCP clients with user-level access control and full audit trails.

→

[Securing MCP server access](mcp-server-access.md)Front your MCP servers with the Identity Gateway so AI agents hold no credentials and every request is audited per user.

→

[Securing your applications' access to LLM APIs](ai-llm-access.md)Eliminate embedded API keys for OpenAI, Anthropic, Azure OpenAI, and other LLM APIs. Control costs and prevent denial of wallet attacks.

→

## Workloads

Applications, services, pipelines, and databases all need credentials to reach the resources they depend on. Aembit issues those credentials just-in-time from verified identity, with no static secrets to store or leak.

### Applications and services

[Credential Management](credential-management.md)Unify access to AWS Secrets Manager, Azure Key Vault, and HashiCorp Vault through a single identity-based policy layer.

→

[Microservices Security](microservices-security.md)Replace shared service accounts with per-workload identity using SPIFFE JWT-SVID or OIDC tokens in your service mesh.

→

### CI/CD and automation

[CI/CD Pipelines](ci-cd.md)Eliminate hardcoded secrets in GitHub Actions, GitLab CI, and Jenkins. No agent required; it uses your platform's OIDC tokens.

→

### Data and analytics

[Database Access](database-access.md)Replace static database passwords with short-lived, identity-based credentials. No changes required on the database side.

→

### Infrastructure and platform

[Multicloud Environments](multicloud.md)Authenticate workloads in AWS, Azure, and GCP using their native identity, with unified policies across all environments.

→

[Third-Party Access](third-party-access.md)Securely connect to external services like GitLab or Snowflake with automatic credential rotation.

→

***

## Quick reference

| If you’re…                                  | Start with                                                              | Complexity  |
| ------------------------------------------- | ----------------------------------------------------------------------- | ----------- |
| Securing AI assistants and MCP clients      | [AI agent access](ai-agents.md)                     | Moderate    |
| Centralizing and auditing MCP server access | [MCP server access](mcp-server-access.md)           | Moderate    |
| Protecting LLM API access                   | [LLM API access](ai-llm-access.md)                  | Quick start |
| Securing CI/CD pipelines                    | [CI/CD Pipelines](ci-cd.md)                         | Quick start |
| Consolidating vault access                  | [Credential Management](credential-management.md)   | Moderate    |
| Securing database access                    | [Database Access](database-access.md)               | Moderate    |
| Running Kubernetes microservices            | [Microservices Security](microservices-security.md) | Moderate    |
| Managing multiple cloud providers           | [Multicloud Environments](multicloud.md)            | Advanced    |
| Connecting to external SaaS APIs            | [Third-Party Access](third-party-access.md)         | Moderate    |

***

## See also

* [How Aembit works](../how-aembit-works.md) - Architecture and deployment model
* [Quickstart](../quickstart/overview.md) - Get started in minutes
* [Security posture](../security-posture/overview.md) - Security architecture and compliance
