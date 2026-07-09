---
type: explanation
title: "Securing third-party access"
description: "How Aembit secures third-party access to your environment"
resource: https://docs.aembit.io/get-started/use-cases/third-party-access/
tags: [use-case]
timestamp: 2026-04-13T13:05:24-07:00
---

# Securing third-party access

Workloads often need to access third-party APIs, most commonly SaaS applications like Salesforce, Slack, GitLab, Snowflake, or Microsoft Graph. Managing credentials for these integrations is challenging because different SaaS providers implement different authentication methods: OAuth 2.0 (two-legged or three-legged), certificate-based auth, API keys, and more. When you need to rotate credentials or restrict access to certain workloads, this variety becomes a significant operational burden.

## What Aembit solves

[Section titled “What Aembit solves”](#what-aembit-solves)

Regardless of your organization’s multicloud posture (all-in on AWS, hybrid Azure/GCP, or still primarily on-premises), you almost certainly have SaaS applications powering your business processes.

Aembit manages third-party credentials as first-class objects and abstracts away provider-specific authentication. When a workload needs to access a third-party service, it authenticates to Aembit using its [workload identity](../concepts/client-workloads.md). Aembit checks your [Access Policies](../concepts/access-policies.md) to determine if this workload can access the service. If approved, Aembit provisions the necessary [credential](../concepts/credential-providers.md) (API key, OAuth token, or service account token) and injects it into the workload. Your application requests access to a SaaS service, and Aembit handles the OAuth flow, API key injection, or certificate exchange. Your code doesn’t change per provider.

Platform teams configure each SaaS provider’s authentication once in Aembit. Developers never see or manage credentials. They authenticate their workload identity, and Aembit handles the rest. Credentials are short-lived, automatically rotated, and scoped to the workloads that need them.

* [API Keys](../../user-guide/access-policies/credential-providers/api-key.md), [OAuth 2.0 tokens](../../user-guide/access-policies/credential-providers/oauth-client-credentials.md), and service account credentials are all first-class objects in Aembit
* Workload scoping ensures only approved services get credentials. [Access Conditions](../concepts/access-conditions.md) can add additional requirements (time-based, geo-based, etc.)
* Aembit automatically rotates credentials, so if someone compromises a key or a key needs replacement, workloads transparently get the new credential on next request
* For SaaS providers not yet supported as built-in credential providers, Aembit supports custom credential provider configurations. Contact support for guidance on custom integrations.

## Why SaaS authentication is complex

[Section titled “Why SaaS authentication is complex”](#why-saas-authentication-is-complex)

Every SaaS provider implements authentication differently. Salesforce supports OAuth 2.0 with JWT bearer tokens or client credentials. Snowflake uses key-pair authentication. Datadog expects API keys with application keys. Even providers that nominally use “OAuth” implement different grant types with different token lifetimes and scoping models. For platform teams managing access to dozens of SaaS APIs, this means learning and maintaining a different authentication flow for each one.

Your cloud IAM (AWS IAM, Azure Entra) handles access to cloud resources well, but it doesn’t help when your workload needs to authenticate to Salesforce or Slack. These SaaS providers have their own authentication systems, and you need to manage credentials for each one independently.

Modern enterprises use dozens or hundreds of SaaS applications, and the applications you’re building need to connect to many of these APIs. The variety of authentication methods across providers multiplies into a significant operational burden at scale.

Examples of authentication method variety across SaaS providers

Different SaaS applications require fundamentally different authentication approaches:

* Salesforce supports OAuth 2.0 two-legged (client credentials flow) or certificate-based authentication (X.509 certs for JWT bearer tokens). Your integration needs to handle both patterns.
* Slack only supports OAuth 2.0 three-legged (authorization code flow with user interaction for initial setup). No two-legged option, no API keys.
* Jira supports API tokens (bearer tokens) for Jira Cloud, but Jira Server/Data Center uses OAuth 1.0a or HTTP Basic Auth with PATs.
* GitLab uses Personal Access Tokens (PATs) or OAuth 2.0 applications, depending on your setup.
* Snowflake uses public key pair authentication (you generate a key pair, register the public key with Snowflake, and use the private key to sign JWTs).

Each of these requires different configuration, different rotation logic, and different security considerations. Across dozens of SaaS apps in a typical environment, this adds up to a significant operational burden.

## Real example: GitLab service account tokens

[Section titled “Real example: GitLab service account tokens”](#real-example-gitlab-service-account-tokens)

Your security team requires strict control over which applications can access GitLab’s API. You maintain lists of Personal Access Tokens (PATs)**Personal Access Token (PAT)**: A long-lived authentication token used as an alternative to passwords for API access. PATs are commonly used with services like GitLab, GitHub, and Jira, and are a frequent target for credential theft. manually, and developers store them in environment variables or configuration files. Rotating them requires coordinating with developers, updating multiple places, and hoping nothing breaks in the process. When someone leaves the team, there’s no reliable way to know which PATs to revoke.

With Aembit, this workflow looks different:

![Internal workload authenticating through Aembit to receive managed GitLab credentials with automatic rotation for third-party SaaS API access](https://docs.aembit.io/d2/docs/get-started/use-cases/third-party-access-0.svg)

Your application authenticates to Aembit using its [workload identity](../concepts/client-workloads.md). This could be a Kubernetes ServiceAccount, AWS IAM role, or any other [supported identity method](../../user-guide/access-policies/client-workloads/identification/overview.md). Aembit verifies this identity.

The application requests credentials to access GitLab. Aembit checks your [Access Policy](../concepts/access-policies.md): “Is this workload allowed to access GitLab? What permissions should it have?”

If approved, Aembit uses the [Managed GitLab Account Credential Provider](../../user-guide/access-policies/credential-providers/managed-gitlab-account.md) to either retrieve an existing Personal Access Token (PAT) or provision a new one. Aembit scopes this credential to exactly what the application needs, replacing broad administrator tokens with least-privilege access.

Aembit injects this PAT into the application at runtime. The application uses it to call GitLab, then Aembit automatically handles rotation on a schedule you define.

If you need to block an application from accessing GitLab, you remove it from the [Access Policy](../concepts/access-policies.md). Next time it requests credentials, Aembit denies the request. No stale PATs left behind, no manual revocation required.

Aembit [logs every credential request](../concepts/audit-report.md), making them auditable for compliance and security investigations.

### Why this matters for third-party access

[Section titled “Why this matters for third-party access”](#why-this-matters-for-third-party-access)

Each SaaS provider implements authentication differently, and your platform team has to learn and maintain each one. Aembit abstracts that complexity. Developers authenticate their workload identity, and Aembit handles the OAuth flow, API key injection, or certificate exchange regardless of the target service.

When someone leaves the team, revoking access means updating an Access Policy, not hunting down PATs across dozens of services. Credential rotation happens automatically on a schedule you define, not when someone remembers to do it.

Is this relevant to your environment?

Ask yourself:

* Are the applications you’re building connecting to SaaS APIs and services?
* How are you handling secrets and authentication for these integrations?
* Are you aware that AWS IAM, Azure Entra, and other cloud IAM solutions don’t help with SaaS authentication?
* Are you using a secrets manager? How often do you rotate these credentials?

## Supported integrations

[Section titled “Supported integrations”](#supported-integrations)

Aembit supports credential management for a wide range of SaaS providers, including Salesforce, Snowflake, Datadog, Slack, Jira, PagerDuty, and GitHub. These cover approximately 80% of common enterprise SaaS integrations. Each integration handles the provider’s specific authentication method (OAuth variants, API keys, certificates) so your application code stays consistent regardless of the target service. See the [authentication methods details](#why-saas-authentication-is-complex) in the preceding section for specifics, or visit the [Credential Providers documentation](../../user-guide/access-policies/server-workloads/overview.md) for the full list of supported integrations and configuration details.

## Next steps

[Section titled “Next steps”](#next-steps)

* Configure your first SaaS credential provider. See [Credential Providers](../../user-guide/access-policies/server-workloads/overview.md) for setup guides by provider.
* For securing LLM API connections specifically (OpenAI, Azure OpenAI, Anthropic), see [AI and LLM Access](ai-llm-access.md)
* For pipelines that push data to SaaS services (Datadog, PagerDuty), see [CI/CD Pipelines](ci-cd.md)
* For workloads accessing SaaS APIs from multiple cloud environments, see [Multicloud Access](multicloud.md)
