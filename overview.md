---
type: reference
title: "Aembit Docs"
description: "Attest. Authenticate. Accelerate."
resource: https://docs.aembit.io/
timestamp: 2026-09-15T18:18:13-07:00
---

# Aembit Docs

NEW

## Aembit Edge SDKs

Aembit now offers Edge SDKs for TypeScript and Python, so your application can attest its workload and retrieve credentials in a few method calls.

[Explore the Edge SDKs](dev-guide/sdk/edge/overview.md)

## Choose your path

### [User Guide](user-guide/overview.md)

Deploy Aembit Edge and the MCP Identity Gateway, build Access Policies for workloads and AI agents, and monitor access.

[Deploy & Install](user-guide/deploy-install/overview.md)

Install Edge Components on Kubernetes, virtual machines, serverless, and CI/CD platforms.

[Agent Controller](user-guide/deploy-install/about-agent-controller.md)[Kubernetes](user-guide/deploy-install/kubernetes/overview.md)[Virtual Machines](user-guide/deploy-install/virtual-machine/overview.md)

[Manage Access Policies](user-guide/access-policies/overview.md)

Define which Client Workloads reach which Server Workloads, and with what credentials.

[Client Workloads](user-guide/access-policies/client-workloads/overview.md)[Trust Providers](user-guide/access-policies/trust-providers/overview.md)[Blended Identity](user-guide/access-policies/blended-identity.md)

[MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md)

Secure AI agent access to MCP servers with Access Policies and credential exchange.

[Overview](user-guide/deploy-install/mcp-identity-gateway/overview.md)[Set up the Gateway](user-guide/access-policies/mcp-identity-gateway/setup-mcp-gateway.md)[Supported servers](user-guide/deploy-install/mcp-identity-gateway/supported-servers/overview.md)

[MCP Authorization Server](user-guide/deploy-install/mcp-authorization-server/overview.md)

OAuth 2.0 authorization for AI agent credential flows.

[Concepts](user-guide/deploy-install/mcp-authorization-server/concepts-mcp-auth-server.md)[Setup](user-guide/access-policies/mcp-authorization-server/setup-mcp-auth-server.md)[Reference](user-guide/deploy-install/mcp-authorization-server/reference-mcp-auth-server.md)

NEW

### [Developer Guide](dev-guide/overview.md)

Integrate from code, commands, or infrastructure. Choose how your workload gets credentials at runtime, then how you manage the configuration behind it.

[Aembit SDKs](dev-guide/sdk/edge/overview.md)

Attest your workload and retrieve credentials from TypeScript or Python.

[Edge SDK overview](dev-guide/sdk/edge/overview.md)[Quickstart](dev-guide/sdk/edge/quickstart.md)[AWS Lambda integration](dev-guide/sdk/edge/integrations/aws-lambda.md)

[Aembit APIs](dev-guide/api/overview.md)

Manage configuration through the Cloud API and retrieve credentials through the Edge API.

[Cloud API](dev-guide/api/cloud/overview.md)[Edge API](dev-guide/api/edge/overview.md)[Authentication](dev-guide/api/edge/auth/overview.md)

[Aembit CLI](dev-guide/cli/overview.md)

Retrieve credentials in CI/CD pipelines using GitLab, GitHub, or OIDC tokens.

[Set up Aembit CLI](dev-guide/cli/usage/setup.md)[Getting credentials](dev-guide/cli/usage/get-credentials.md)[Command reference](dev-guide/cli/reference/overview.md)

[Agent Proxy](dev-guide/integration/agent-proxy.md)

Intercept your workload's traffic so its source needs no changes.

[Integrate through Agent Proxy](dev-guide/integration/agent-proxy.md)[Local development](dev-guide/integration/local-development.md)[Test and debug](dev-guide/integration/testing.md)

### [Get Started](get-started/overview.md)

Learn what Aembit does, how workload identity works, and set up your first secure connection.

[What is Aembit?](get-started/overview.md)[Core concepts](get-started/concepts/overview.md)[Quickstart](get-started/quickstart/overview.md)

### [Reference](reference/overview.md)

Look up supported platforms, identifier formats, and Edge Component versions.

[Support Matrix](reference/support-matrix.md)[Identifiers](reference/identifiers.md)[Edge Component versions](reference/edge-components/edge-component-supported-versions.md)

## Find your focus

Select your role

Security TeamsPlatform EngineersDevelopersIT AdminsExecutives

### Control & Audit

Security policies, compliance monitoring, and audit tools

#### Access Control

[Access Policies](user-guide/access-policies/overview.md)[Agentic AI Blended Identities](user-guide/access-policies/blended-identity.md)[MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md)[Client Workloads](user-guide/access-policies/client-workloads/overview.md)[Server Workloads](user-guide/access-policies/server-workloads/overview.md)[Trust Providers](user-guide/access-policies/trust-providers/overview.md)[Credential Providers](user-guide/access-policies/credential-providers/overview.md)

#### Monitoring

[Authorization Events](user-guide/audit-report/access-authorization-events.md)[Audit Logs](user-guide/audit-report/audit-logs.md)[Workload Activity](user-guide/audit-report/workload-events/overview.md)[Log Streams](user-guide/administration/log-streams/overview.md)[Dashboard](user-guide/audit-report/overview.md)

#### Compliance

[Security Compliance](get-started/security-posture/security-compliance.md)[Security Posture](get-started/security-posture/overview.md)[Global Policy](user-guide/administration/global-policy/overview.md)[Threat Model](get-started/security-posture/threat-model.md)

#### Quick Actions

[Create Policy](user-guide/access-policies/overview.md)[View Audit Log](user-guide/audit-report/audit-logs.md)[Generate Report](user-guide/audit-report/overview.md)[Export Data](dev-guide/api/overview.md)

### Deploy & Scale

Infrastructure deployment, scaling, and operations

#### Deployment

[Kubernetes](user-guide/deploy-install/kubernetes/overview.md)[AWS ECS Fargate](user-guide/deploy-install/serverless/aws-ecs-fargate.md)[AWS Lambda](user-guide/deploy-install/serverless/overview.md)[VM Installation](user-guide/deploy-install/virtual-machine/overview.md)[MCP Identity Gateway Setup](user-guide/access-policies/mcp-identity-gateway/setup-mcp-gateway.md)

#### Infrastructure

[Terraform Provider](user-guide/access-policies/advanced-options/terraform/terraform-configuration.md)[Helm Charts](user-guide/deploy-install/kubernetes/overview.md)[Network Config](user-guide/deploy-install/overview.md)[TLS Decrypt](user-guide/deploy-install/advanced-options/tls-decrypt/overview.md)

#### Operations

[Log Streams](user-guide/administration/log-streams/overview.md)[Log Levels](user-guide/deploy-install/advanced-options/changing-agent-log-levels.md)[Troubleshooting](user-guide/troubleshooting/overview.md)[Changelog](https://docs.aembit.io/changelog/)

#### Reference

[Support Matrix](reference/support-matrix.md)[Architecture](get-started/how-aembit-works.md)[Environment Variables](reference/edge-components/edge-component-env-vars.md)[Helm Chart Options](reference/edge-components/helm-chart-config-options.md)

### Build & Integrate

SDKs, APIs, and integration guides for developers

#### Integration

[Developer Guide](dev-guide/overview.md)[Edge SDK](dev-guide/sdk/edge/overview.md)[Agent Proxy Integration](dev-guide/integration/agent-proxy.md)[Architecture Patterns](user-guide/access-policies/server-workloads/architecture-patterns.md)[Server Workload Guides](user-guide/access-policies/server-workloads/guides/overview.md)[CLI Get Credentials](dev-guide/cli/usage/get-credentials.md)

#### APIs

[REST API Reference](dev-guide/api/overview.md)[Cloud API](dev-guide/api/cloud/overview.md)[Edge API](dev-guide/api/edge/overview.md)[Authentication](dev-guide/api/edge/auth/overview.md)

#### CI/CD

[GitHub Actions](user-guide/deploy-install/ci-cd/github/overview.md)[GitLab CI](user-guide/deploy-install/ci-cd/gitlab/overview.md)[Jenkins Pipelines](user-guide/deploy-install/ci-cd/jenkins-pipelines.md)[CI/CD Overview](user-guide/deploy-install/ci-cd/overview.md)

#### AI Integration

[MCP Server](user-guide/mcp-server/overview.md)[MCP Authorization Server](user-guide/deploy-install/mcp-authorization-server/overview.md)[MCP Identity Gateway](user-guide/deploy-install/mcp-identity-gateway/overview.md)[Agentic AI Blended Identities](user-guide/access-policies/blended-identity.md)

### Manage & Configure

User management, SSO, and tenant configuration

#### User Management

[Users](user-guide/administration/users/overview.md)[Roles](user-guide/administration/roles/overview.md)[Identity Providers](user-guide/administration/identity-providers/overview.md)[Agentic AI Blended Identities](user-guide/access-policies/blended-identity.md)

#### Tenant

[Administration](user-guide/administration/overview.md)[Resource Sets](user-guide/administration/resource-sets/overview.md)[Sign-On Policy](user-guide/administration/sign-on-policy/overview.md)

#### Security

[Audit Logs](user-guide/audit-report/audit-logs.md)[Global Policy](user-guide/administration/global-policy/overview.md)

#### Quick Actions

[Add User](user-guide/administration/users/add-user.md)[Configure SSO](user-guide/administration/identity-providers/overview.md)

### Overview & Value

High-level insights and business value

#### Overview

[What is Aembit](get-started/overview.md)[How it Works](get-started/how-aembit-works.md)[Use Cases](get-started/use-cases/overview.md)[Securing AI Agents](get-started/use-cases/ai-agents.md)

#### Value

[Security Posture](get-started/security-posture/overview.md)[Business Use Cases](get-started/use-cases/overview.md)

#### Reports

[Audit & Compliance](user-guide/audit-report/overview.md)[Global Policy](user-guide/audit-report/global-policy.md)

#### Quick Actions

[View Dashboard](user-guide/audit-report/overview.md)[Get Started](get-started/overview.md)

## How Aembit works

AI AgentsMulticloudDatabase Access

WorkloadsIdentity & TrustPolicy EvaluationCredential Injection

#### Learn more

[Client Workloads](get-started/concepts/client-workloads.md)[Server Workloads](get-started/concepts/server-workloads.md)

#### Start building

[Configure Client Workloads](user-guide/access-policies/client-workloads/overview.md)[MCP Server setup](user-guide/mcp-server/overview.md)

#### Learn more

[Blended Identity](user-guide/access-policies/blended-identity.md)[Trust Providers](get-started/concepts/trust-providers.md)

#### Start building

[MCP Authorization Server](user-guide/deploy-install/mcp-authorization-server/overview.md)

#### Learn more

[Access Policies](get-started/concepts/access-policies.md)[Access Conditions](get-started/concepts/access-conditions.md)

#### Start building

[Configure Access Policies](user-guide/access-policies/overview.md)

#### Learn more

[Credential Providers](get-started/concepts/credential-providers.md)

#### Start building

[MCP User-Based Access Token](user-guide/access-policies/credential-providers/mcp-user-based-access-token.md)

![AI Agents architecture diagram](https://docs.aembit.io/diagrams/use-case-explorer/ai-agents/base.svg)![AI Agents - Workloads highlighted](https://docs.aembit.io/diagrams/use-case-explorer/ai-agents/workloads.svg)![AI Agents - Identity & Trust highlighted](https://docs.aembit.io/diagrams/use-case-explorer/ai-agents/identity.svg)![AI Agents - Policy Evaluation highlighted](https://docs.aembit.io/diagrams/use-case-explorer/ai-agents/policy.svg)![AI Agents - Credential Injection highlighted](https://docs.aembit.io/diagrams/use-case-explorer/ai-agents/credentials.svg)

WorkloadsIdentity TranslationAccess PoliciesCross-Cloud Credentials

#### Learn more

[Client Workloads](get-started/concepts/client-workloads.md)[Server Workloads](get-started/concepts/server-workloads.md)

#### Start building

[Deploy Aembit Edge](user-guide/deploy-install/overview.md)

#### Learn more

[Trust Providers](get-started/concepts/trust-providers.md)

#### Start building

[AWS Role Trust Provider](user-guide/access-policies/trust-providers/aws-role-trust-provider.md)[Azure Entra WIF Credential Provider](user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md)

#### Learn more

[Access Policies](get-started/concepts/access-policies.md)[Access Conditions](get-started/concepts/access-conditions.md)

#### Start building

[Configure Access Policies](user-guide/access-policies/overview.md)

#### Learn more

[Credential Providers](get-started/concepts/credential-providers.md)

#### Start building

[Azure Entra WIF](user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md)[GCP Workload Identity](user-guide/access-policies/credential-providers/google-workload-identity-federation.md)

![Multicloud architecture diagram](https://docs.aembit.io/diagrams/use-case-explorer/multicloud/base.svg)![Multicloud - Workloads highlighted](https://docs.aembit.io/diagrams/use-case-explorer/multicloud/workloads.svg)![Multicloud - Identity Translation highlighted](https://docs.aembit.io/diagrams/use-case-explorer/multicloud/identity.svg)![Multicloud - Access Policies highlighted](https://docs.aembit.io/diagrams/use-case-explorer/multicloud/policy.svg)![Multicloud - Cross-Cloud Credentials highlighted](https://docs.aembit.io/diagrams/use-case-explorer/multicloud/credentials.svg)

WorkloadsCloud IAM TrustAccess PoliciesDatabase Credentials

#### Learn more

[Client Workloads](get-started/concepts/client-workloads.md)[Server Workloads](get-started/concepts/server-workloads.md)

#### Start building

[Configure Client Workloads](user-guide/access-policies/client-workloads/overview.md)

#### Learn more

[Trust Providers](get-started/concepts/trust-providers.md)

#### Start building

[AWS Role Trust Provider](user-guide/access-policies/trust-providers/aws-role-trust-provider.md)

#### Learn more

[Access Policies](get-started/concepts/access-policies.md)[Access Conditions](get-started/concepts/access-conditions.md)

#### Start building

[Configure Access Policies](user-guide/access-policies/overview.md)

#### Learn more

[Credential Providers](get-started/concepts/credential-providers.md)

#### Start building

[PostgreSQL guide](user-guide/access-policies/server-workloads/guides/aws-postgres.md)[Snowflake guide](user-guide/access-policies/server-workloads/guides/snowflake.md)

![Database Access architecture diagram](https://docs.aembit.io/diagrams/use-case-explorer/database/base.svg)![Database Access - Workloads highlighted](https://docs.aembit.io/diagrams/use-case-explorer/database/workloads.svg)![Database Access - Cloud IAM Trust highlighted](https://docs.aembit.io/diagrams/use-case-explorer/database/identity.svg)![Database Access - Access Policies highlighted](https://docs.aembit.io/diagrams/use-case-explorer/database/policy.svg)![Database Access - Database Credentials highlighted](https://docs.aembit.io/diagrams/use-case-explorer/database/credentials.svg)
