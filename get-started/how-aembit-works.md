---
type: explanation
title: "How Aembit works"
description: "A simplified description of how Aembit works, including its architecture and components"
resource: https://docs.aembit.io/get-started/how-aembit-works/
timestamp: 2026-06-15T10:19:43-07:00
type_inferred: true
---

# How Aembit works

In modern technical environments, applications, services, scripts, APIs, and AI agents**AI Agent**: A software workload that authenticates to systems, requests credentials, and accesses resources, either on behalf of a person or on its own. Aembit secures AI agents with the same identity-first model it uses for any workload. User-driven agents such as Claude Desktop also carry a blended identity that ties access to both the user and the agent.[Learn more](use-cases/ai-agents.md) frequently need to communicate with each other. They also access shared resources like databases, SaaS platforms, and other internal services. These automated systems operating without direct human interaction are **Non-Human Identities (NHI)**, commonly called **workloads**. AI agents are a distinct class of non-human identity. Whether a user-driven assistant like Claude Desktop or an autonomous service, they authenticate and reach systems through APIs, databases, and MCP servers the same way other workloads do.

Use the links in each section to dive deeper into specific topics related to how Aembit works or start configuring and using those features.

## The core problem Aembit solves

[Section titled “The core problem Aembit solves”](#the-core-problem-aembit-solves)

Most organizations secure workload access using static, long-lived secrets (API keys, passwords, tokens) that are:

* Difficult to securely distribute and store
* Prone to leakage and theft
* Hard to rotate
* A significant security risk when compromised

AI agents compound this problem in two ways. First, the MCP servers that connect agents to your systems often store long-lived credentials. Second, a user-driven agent inherits its operator’s full rights, with no least-privilege boundary at the agent layer. Aembit resolves this with [blended identity](../ai-guide/blended-identity.md), evaluating the user and the agent together in every access decision.

## Introducing Agentic AI and Workload IAM

[Section titled “Introducing Agentic AI and Workload IAM”](#introducing-agentic-ai-and-workload-iam)

Aembit solves these challenges with its Workload Identity and Access Management (Workload IAM) platform, which secures two forms of non-human identity under one identity-first model.

**Traditional workloads** are the applications, services, scripts, and APIs that run your business. Examples include a microservice calling an API, a script accessing a database, or a CI/CD job deploying to a cloud provider.

**AI agents** are a form of non-human identity in their own right. A user-driven assistant like Claude Desktop or an autonomous agent reaches tools and data through APIs, databases, and MCP servers.

Both forms interact the same way: one workload (a Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](concepts/client-workloads.md)) initiates a request to access another workload or service (a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](concepts/server-workloads.md)). A Client Workload might be a microservice calling a payments API or an AI agent calling an MCP server, and the Server Workload is whatever it needs to reach.

Aembit shifts authentication away from what a workload knows (static secrets) toward who a workload verifiably is, using evidence from its environment and context. Instead of using a traditional password or API key, Aembit verifies a workload’s identity cryptographically using evidence from its runtime environment, such as:

* Where the workload is running
* What platform issued the workload’s identity
* Cloud instance metadata
* Kubernetes service account tokens
* SPIFFE Verifiable Identity Documents (SVID)

![Simplified Workload IAM overview showing a Client Workload requesting access through Aembit to a Server Workload](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-0.svg)

### Client Workloads

[Section titled “Client Workloads”](#client-workloads)

Client Workloads are the initiators of requests to access Server Workloads. A Client Workload can be any service, API, script, or AI agent that needs to reach another service, API, or resource.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Client Workloads ](concepts/client-workloads.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Client Workloads ](../user-guide/access-policies/client-workloads/overview.md)See the Aembit User Guide

→

### Server Workloads

[Section titled “Server Workloads”](#server-workloads)

Server Workloads are the target of Client Workload requests. A Server Workload can be any service, API, database, or MCP server that a Client Workload needs to access.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Server Workloads ](concepts/server-workloads.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Server Workloads ](../user-guide/access-policies/server-workloads/overview.md)See the Aembit User Guide

→

### Blended identity for AI agents

[Section titled “Blended identity for AI agents”](#blended-identity-for-ai-agents)

An AI agent acts as a Client Workload, so Aembit’s model already secures it. User-driven agents add one dimension: a second identity in every request, the human operating the agent. Aembit’s blended identity**Blended Identity**: An access model that combines a human user's identity (authenticated through an Identity Provider) with an AI agent's workload identity into a single access decision, enabling policies that evaluate both "who is this user" and "which agent are they using" simultaneously.[Learn more](../ai-guide/blended-identity.md) model combines the user’s identity (validated through your identity provider by a Trust Provider) with the agent’s workload identity**Workload Identity**: A unique, verifiable identity assigned to a workload by Aembit.[Learn more](https://docs.aembit.io/get-started/concepts/how-aembit-works/#introducing-workload-iam). It then evaluates both in a single Access Policy. This lets a policy express not just “is this a trusted agent?” but “is this specific user, using this specific agent, allowed to access this resource?” Aembit treats autonomous agents that run without a human as standard workloads, securing them with the same model as any other Client Workload.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[Understanding blended identity ](../ai-guide/blended-identity.md)See the AI Guide

→

![](https://docs.aembit.io/aembit-icons/shield-keyhole-solid.svg)

[Securing AI agent access ](use-cases/ai-agents.md)See the use case

→

## Secure workloads with Access Policies

[Section titled “Secure workloads with Access Policies”](#secure-workloads-with-access-policies)

Aembit manages workload-to-workload access through Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](concepts/access-policies.md). Access Policies serve as the central control mechanism to define **who** (which Client Workload) **can access what** (which Server Workload) **under what conditions**. This policy-driven approach replaces the need for Client Workloads to possess static secrets for every service they need to access.

Instead of relying on secrets embedded in the client, Access Policies work by leveraging the inherent identity of the workload. Aembit verifies a Client Workload’s identity from its runtime environment. It then provisions the necessary credentials Just-In-Time (JIT) to the Server Workload it’s trying to access.

![Simplified Access Policy showing Client Workload, Access Policy evaluation, and Server Workload](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-1.svg)

Access Policies link a specific Client Workload to a specific Server Workload and define the security checks required for access.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Access Policies ](concepts/access-policies.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Access Policies ](../user-guide/access-policies/overview.md)See the Aembit User Guide

→

The components of an Access Policy include:

* A Client Workload (who wants access)
* A Server Workload (what they want to access)
* A Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](concepts/trust-providers.md) (how to verify the client’s identity)
* Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](concepts/access-conditions.md) (when/where/under what circumstances to allow access)
* A Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](concepts/credential-providers.md) (what credentials to issue)

The following sections describe these key components of an Access Policy:

### Trust Providers

[Section titled “Trust Providers”](#trust-providers)

Trust Providers are fundamental to Aembit’s “secretless” approach. Trust Providers **cryptographically verify the identity** of Client Workloads *without* clients needing a pre-shared secret to authenticate itself to Aembit.

Trust Providers authenticate the workload’s identity by examining verifiable evidence from its environment, such as cloud instance metadata, Kubernetes service account tokens, or OIDC tokens from CI/CD platforms.

![Simplified Trust Provider identity verification within an Access Policy](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-2.svg)

Aembit calls this Workload Attestation**Workload Attestation**: Workload attestation cryptographically verifies a workload's identity using evidence from its runtime environment, such as platform identity documents or tokens, rather than using static credentials.[Learn more](concepts/trust-providers.md). If the Trust Provider can’t verify the workload’s identity, Aembit denies access to the Server Workload.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Trust Providers ](concepts/trust-providers.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Trust Providers ](../user-guide/access-policies/trust-providers/overview.md)See the Aembit User Guide

→

Once Aembit successfully verifies the identity of a Client Workload through a Trust Provider it goes to the next step in the Access Policy Evaluation flow: Access Conditions.

### Access Conditions

[Section titled “Access Conditions”](#access-conditions)

Once a Client Workload’s identity is successfully verified by a Trust Provider, Aembit evaluates any Access Conditions you may have defined in the Access Policy. Access Conditions add **contextual checks** to the access decision. You can enforce rules based on factors like the time of day, geographic location (GeoIP), or the security posture of the workload’s host. Aembit derives that posture from integrations with tools like Wiz or CrowdStrike.

![Simplified Access Conditions evaluation with Trust Provider, context sources, and GeoIP/security posture checks](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-3.svg)

All Access Conditions you configure must evaluate successfully for authorization to proceed. This provides a level of dynamic, risk-adaptive security, providing a Multi-Factor Authentication (MFA)-like strength for non-human access.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Access Conditions ](concepts/access-conditions.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Access Conditions ](../user-guide/access-policies/access-conditions/overview.md)See the Aembit User Guide

→

Once Aembit successfully verifies the context of a Client Workload through Access Conditions it goes to the next step in the Access Policy Evaluation flow: Credential Provider.

### Credential Providers

[Section titled “Credential Providers”](#credential-providers)

If Aembit verifies a Client Workload’s identity by using a Trust Provider and the Client Workload meets all Access Conditions, Aembit then invokes the necessary **Credential Provider**. The role of the Credential Provider is to **obtain the specific access credential** required by the target Server Workload.

This could involve interacting with systems like cloud Security Token Services (AWS STS, Azure WIF, Google WIF), OAuth servers, or internal credential stores. From these, Aembit gets a short-lived token, API key, or other required secret.

![Simplified Credential Provider flow showing just-in-time credential retrieval from external services](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-4.svg)

Credential Providers abstract away the complexity of how the target Server Workload expects to authenticate Client Workloads.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Credential Providers ](concepts/credential-providers.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Credential Providers ](../user-guide/access-policies/credential-providers/overview.md)See the Aembit User Guide

→

## Aembit’s architecture

[Section titled “Aembit’s architecture”](#aembits-architecture)

Aembit’s two main architectural components, Aembit Cloud and Aembit Edge, work together to execute its identity-first, policy-driven access flow.

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](concepts/aembit-cloud.md) is Aembit’s **centralized control plane**, where all the configuration and policy management occurs. Aembit Cloud is where you define and manage your Client Workloads, Server Workloads, Access Policies, Trust Providers, Access Conditions, and Credential Providers.

Aembit Cloud receives requests from Aembit Edge (more on that in the next section), and performs Access Policy decision-making logic and administrative tasks such as:

* authenticating Client Workloads using Trust Providers
* evaluating Access Conditions
* interacting with Credential Providers to obtain necessary credentials
* centralizes all access event logs for auditing and visibility

It then sends the authorization decision and any credentials back to Aembit Edge.

![Simplified Aembit Cloud architecture showing Access Policy evaluation, trust verification, credential retrieval, and authorization logging](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-5.svg)

Aembit Cloud is explicitly designed *not* to process or log the actual application data exchanged between workloads; it only handles metadata related to the access control decision.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Aembit Cloud ](concepts/aembit-cloud.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Aembit Cloud ](../user-guide/access-policies/overview.md)See the Aembit User Guide

→

### Aembit Edge

[Section titled “Aembit Edge”](#aembit-edge)

Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](concepts/aembit-edge.md) is Aembit’s **distributed data plane** and **enforcement point**, deployed directly within your environments, close to your workloads. Aembit Edge’s primary job is to transparently intercept outbound network requests from Client Workloads destined for Server Workloads.

Upon interception, Aembit Edge gathers identity evidence from its local runtime environment, communicates with Aembit Cloud for authentication, policy evaluation, and credential retrieval. Once Aembit authenticates a Client Workload’s identity, Aembit Edge **injects the credential just-in-time (JIT)** into the Client Workload’s original request. Aembit Edge then forwards the request to the target Server Workload.

![Simplified Aembit Edge proxy flow showing request interception, Aembit Cloud communication, and just-in-time credential injection](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-6.svg)

If Aembit Cloud denies a request, Aembit Edge blocks it. This interception and injection capability allows Aembit to secure access for many existing applications without requiring code changes (“no-code auth”). Aembit Edge also sends detailed access event logs back to the Cloud.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Aembit Edge ](concepts/aembit-edge.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Deploy Aembit Edge ](../user-guide/deploy-install/overview.md)See the Aembit User Guide

→

### Specialized enforcement for AI agents

[Section titled “Specialized enforcement for AI agents”](#specialized-enforcement-for-ai-agents)

Aembit Cloud and Aembit Edge secure access for traditional workloads and AI agents alike. AI agents that connect over MCP add two specialized enforcement surfaces, rather than a separate deployed component:

* The **MCP Authorization Server** performs OAuth 2.1**OAuth 2.1**: An updated consolidation of the OAuth 2.0 specification that mandates PKCE for all clients, removes the implicit grant and resource owner password grant, and requires stricter security defaults.[Learn more(opens in new tab)](https://oauth.net/2.1/) authorization for MCP clients and runs inside Aembit Cloud.
* The **MCP Identity Gateway** is a transparent proxy that enforces Access Policies and injects credentials, so the agent never holds them directly.

Both apply the same Access Policy model described in this guide.

![](https://docs.aembit.io/aembit-icons/shield-keyhole-solid.svg)

[MCP Authorization Server ](../ai-guide/mcp/authorization-server/overview.md)OAuth 2.1 authorization for MCP clients

→

![](https://docs.aembit.io/aembit-icons/aembit-edge.svg)

[MCP Identity Gateway ](../ai-guide/mcp/identity-gateway/overview.md)Transparent proxy for MCP traffic

→

## Logging and auditing

[Section titled “Logging and auditing”](#logging-and-auditing)

Aembit provides **comprehensive, centralized logging and auditing** critical for security and visibility. Its logging is identity-centric, linking events to verified workload or administrator identities. Aembit’s logging capabilities include recording workload access attempts or Access Authorization Events and administrative actions.

You can export logs using **Log Streams** to external destinations like **AWS S3** and **Google Cloud Storage** for retention and integration with SIEM platforms.

![Simplified logging flow showing Aembit Cloud writing to the Authorization Log with Log Stream export to AWS S3 and Google Cloud Storage](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-7.svg)

Aembit’s logging directly supports **compliance requirements**, by generating detailed, identity-based audit records. It also aids **security incident response and forensic analysis** by providing clear context and attribution for workload activities.

For AI-agent use cases, each access event carries both the agent’s workload identity and the user’s identity, so you can separate what an agent did from who directed it. See [blended identity and audit](../ai-guide/blended-identity.md#blended-identity-and-audit).

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Auditing ](concepts/audit-report.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Audit Aembit logs ](../user-guide/audit-report/overview.md)See the Aembit User Guide

→

## Access Policy flow: Putting it all together

[Section titled “Access Policy flow: Putting it all together”](#access-policy-flow-putting-it-all-together)

Putting all these components together, Aembit provides a powerful and flexible solution for managing workload access without the need for static secrets.

The following simplified Access Policy evaluation flow illustrates how all Aembit’s components work together to provide secure workload access:

1. **Request Initiation and Interception** - A Client Workload attempts to connect to a Server Workload.

2. **Identify the Workloads** - Aembit Edge observes the Client Workload’s identity using metadata from your environment, such as Kubernetes service account names, VM identity tokens, or cloud-specific signals.

3. **Match request to an Access Policy** - Aembit Cloud compares the request to existing Access Policies. If no policy matches both workloads, Aembit denies the request.

4. **Verify Identity with Trust Providers** (optional) - Aembit checks with a Trust Provider (like AWS, Azure, or Kubernetes) to verify the Client Workload’s identity. This process removes the need for long-lived secrets by leveraging native cloud or orchestration signals.

5. **Evaluate Access Conditions** (optional) - If the request matches a policy, Aembit checks whether it satisfies any extra conditions. For example, it might require the workload to run in a specific region or during certain hours.

6. **Retrieve Credentials from a Credential Provider** - When the request passes all checks, Aembit contacts the Credential Provider to retrieve the appropriate credential, such as an API key or OAuth token.

7. **Inject the Credential** - Aembit Edge injects the credential directly into the request, typically using an HTTP header. The Client Workload never sees or stores the credential.

The following diagram is a simplified illustration of the Access Policy evaluation flow:

![End-to-end Access Policy evaluation flow from Client Workload request through Aembit Edge and Cloud to authenticated Server Workload access](https://docs.aembit.io/d2/docs/get-started/how-aembit-works-8.svg)

AI-agent and MCP access follows this same policy model, with one addition. Before issuing a credential, the Access Policy evaluates both the agent’s workload identity and the user’s identity. The agent-specific flow is already diagrammed in [securing AI agent access](use-cases/ai-agents.md) and the [MCP Identity Gateway concepts](../ai-guide/mcp/identity-gateway/concepts-mcp-gateway.md).

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Conceptual overview](concepts/overview.md)
* [Access Policies](concepts/access-policies.md)
* [Audit and report](concepts/audit-report.md)
* [Administering Aembit](concepts/administration.md)
* [Scaling with Terraform](concepts/scaling-terraform.md)
