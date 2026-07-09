---
type: explanation
title: "Securing your applications' access to LLM APIs"
description: "How Aembit protects workload connections to LLM APIs like OpenAI, Azure OpenAI, and Anthropic"
resource: https://docs.aembit.io/get-started/use-cases/ai-llm-access/
tags: [use-case]
timestamp: 2026-06-15T10:19:43-07:00
---

# Securing your applications' access to LLM APIs

Applications that call LLM APIs like OpenAI, Anthropic, or Azure OpenAI typically embed static API keys in code.

Exfiltrated or misused keys let attackers consume API services at the victim’s expense. This attack pattern is called LLMjacking**LLMjacking**: The unauthorized use of stolen LLM API credentials to consume AI services at the victim's expense. Attackers obtain API keys through exposed repositories, compromised environments, or leaked credentials, then resell access or use it directly.[Learn more(opens in new tab)](https://sysdig.com/blog/llmjacking-stolen-cloud-credentials-used-in-new-ai-attack/).

Stolen credentials sell for as little as $30, and a single compromised key can generate significant unexpected charges in hours.

LLMjacking and denial of wallet attacks

**LLMjacking** is the unauthorized use of stolen LLM API credentials to consume AI services at the victim’s expense. Attackers obtain API keys through exposed repositories, compromised environments, or leaked credentials, then resell access or use it directly. Sysdig reported a 10x increase in LLMjacking attacks in mid-2024, and Pillar Security documented 35,000 attack sessions targeting exposed AI infrastructure between December 2025 and January 2026.

LLMjacking is a form of **denial of wallet (DoW) attack**, a broader class of attacks that exploit consumption-based billing models to inflict financial damage. Unlike traditional denial-of-service attacks that crash systems, denial of wallet**Denial of wallet**: An attack that exploits consumption-based billing models to inflict financial damage through excessive resource usage. Unlike denial-of-service attacks that crash systems, denial of wallet attacks drain budgets. attacks drain your budget through excessive resource consumption.

LLM APIs are particularly susceptible because of how they’re priced and provisioned:

* LLM API calls cost markedly more than typical cloud API calls
* Token-based pricing means long responses or large context windows multiply costs
* Many LLM providers allow unlimited usage with pay-as-you-go billing and no built-in rate limits

As an example, one experimental AI agent project making unconstrained calls to GPT-4 ran up about $4M-$15M in bills in a single week.

## What Aembit solves

[Section titled “What Aembit solves”](#what-aembit-solves)

Aembit eliminates embedded API keys and brings identity-based access control to LLM API access. Instead of distributing static credentials to applications, Aembit authenticates workloads and issues short-lived credentials based on your Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md) rules.

* Applications request temporary credentials from Aembit instead of storing static API keys in code or configuration files.
* Access Policies enforce conditional access to prevent unauthorized or runaway LLM API usage that generates unexpected bills.
* Aembit logs every access request with verified workload identity, so you can track which workload accessed which LLM API, when, and under what conditions.
* Protecting LLM APIs uses the same Aembit pattern as any other API. If you’re already using Aembit for other resources, you don’t need any new features.
* Aembit verifies workload identity before granting access, replacing static keys with cryptographically attested identity.

LLM API credential vulnerabilities

Most LLM APIs use bearer token**Bearer token**: An authentication credential passed in HTTP headers where anyone who possesses the token can use it to access the protected resource. No additional proof of identity is required beyond holding the token itself. authentication, which means basic API keys passed in HTTP headers. This is one of the most rudimentary authentication methods.

* Keys often remain valid indefinitely until manually revoked.
* A single key typically grants full account access to all models and endpoints.
* The credential itself provides no protection against excessive usage.
* The API provider has no way to know which workload or user is actually making the call.

An exfiltrated key is functionally identical to a legitimate one. Without external controls like Aembit, there’s no way to distinguish between authorized and unauthorized usage until the bill arrives.

## Real example: Application accessing OpenAI API

[Section titled “Real example: Application accessing OpenAI API”](#real-example-application-accessing-openai-api)

Your organization runs a customer support chatbot that calls OpenAI’s GPT-4 API. Without Aembit, you’d need to:

1. Create an OpenAI API key
2. Store that key in your application’s environment variables or secrets manager
3. Manually rotate the key periodically and update all consuming applications
4. Accept that any compromise of your application, deployment pipeline, or secrets storage exposes the key

With Aembit, this flow looks different:

![Application accessing OpenAI API through Aembit with identity verification, policy evaluation, and temporary credential issuance](https://docs.aembit.io/d2/docs/get-started/use-cases/ai-llm-access-0.svg)

Application requests credentials from Aembit instead of storing them directly. Access Policy enforces which workloads can call the LLM API and under what conditions.

The flow works like this:

1. Your application needs to call the OpenAI API
2. [Aembit Edge](../concepts/aembit-edge.md) intercepts the request and asks Aembit for credentials
3. Aembit verifies the workload’s identity using a [Trust Provider](../concepts/trust-providers.md) (AWS IAM role, Kubernetes service account, Azure managed identity, etc.)
4. If the Access Policy allows this workload to access OpenAI, Aembit retrieves or generates the appropriate credential
5. Aembit returns a temporary credential to the application
6. The application uses that credential to call the OpenAI API

The application makes a standard HTTP request to OpenAI. Aembit handles authentication and authorization transparently, so your application code doesn’t change.

### Why this matters for AI and LLM access

[Section titled “Why this matters for AI and LLM access”](#why-this-matters-for-ai-and-llm-access)

LLM APIs bill per token and request, making them prime targets for LLMjacking. Attackers use stolen credentials to consume AI services at the victim’s expense. One experimental agent project ran up about $4M-$15M in bills in a single week of operation. Credential protection for LLM APIs has a direct budget impact.

While some LLM providers offer more sophisticated authentication options, the most common pattern across the LLM ecosystem remains static API keys with rudimentary security. Aembit upgrades this security model without requiring LLM providers to change anything.

Connecting to an LLM API is the same fundamental pattern as connecting to any other resource. If you’re already using Aembit for other resources, you can protect LLM API access immediately with the same capabilities, without any new features.

When an application makes thousands of LLM API calls, compliance, finance, and security teams need to know which workload was responsible. Aembit logs every access request with verified workload identity, creating an audit trail that ties API usage to specific applications.

## Supported LLM providers

[Section titled “Supported LLM providers”](#supported-llm-providers)

Aembit works with any LLM API that accepts standard authentication mechanisms like API keys, OAuth tokens, or bearer tokens. The integration is credential-provider-specific, not provider-specific. Configure Aembit with the appropriate [Credential Provider](../concepts/credential-providers.md) for your LLM service, and Aembit handles the rest.

Common LLM providers include:

* OpenAI (GPT-4, ChatGPT API), using API key authentication
* Anthropic (Claude API), using Workload Identity Federation or API key authentication
* Azure OpenAI Service, using Azure Entra managed identities or API keys
* Google Vertex AI (Gemini API), using GCP Workload Identity Federation
* AWS Bedrock, using AWS STS Federation
* Any HTTP-based LLM API that uses API keys, OAuth, or bearer tokens

See the [Credential Provider documentation](../concepts/credential-providers.md) for configuration details specific to your LLM provider’s authentication method.

## Next steps

[Section titled “Next steps”](#next-steps)

* [Learn about Access Policies](../concepts/access-policies.md) to control which workloads can access LLM APIs and under what conditions
* [Explore AI agent scenarios](ai-agents.md) where AI agents themselves become workloads accessing enterprise resources via the Model Context Protocol (MCP)
* [Review Trust Provider options](../concepts/trust-providers.md) to verify workload identity using your environment’s native identity system
* [Secure third-party SaaS API access](third-party-access.md) beyond LLMs, including Salesforce, Slack, Snowflake, and other SaaS applications
