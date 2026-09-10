---
type: explanation
title: "About Access Policies"
description: "Description of Access Policies, their components, and how the evaluation flow works"
resource: https://docs.aembit.io/get-started/concepts/access-policies/
tags: ["concept"]
timestamp: 2026-09-09T08:20:13-07:00
---

# About Access Policies

Access Policies are the central mechanism within Aembit. Access Policies define, enforce, and audit access between Non-Human Identities (NHI), such as applications, scripts, services, and infrastructure components.

The fundamental purpose of Access Policies is to govern workload-to-workload interactions. They do by cryptographically verifying workload identity and contextual factors, rather than relying on the distribution and management of static secrets. This approach aims to deliver granular, dynamic, and continuously verifiable control over NHI access, enhancing security posture and simplifying operations in complex, distributed environments.

This topic provides high-level details of Aembit Access Policies, focusing on their core components and the intricate interplay between the components during Access Policy evaluation.

## Access Policy components

Click each link card to learn more details about each Access Policy component.

![](https://docs.aembit.io/aembit-icons/client-workload.svg)

[Client Workloads](../../user-guide/access-policies/client-workloads/overview.md) are any non-human entity that initiates an access request to consume a service or resource provided by a Server Workload.

→

![](https://docs.aembit.io/aembit-icons/server-workload.svg)

[Server Workloads](../../user-guide/access-policies/server-workloads/overview.md) are software applications that serve requests from Client Workloads such as third-party SaaS APIs, API gateways, databases, and data warehouses.

→

![](https://docs.aembit.io/aembit-icons/trust-provider.svg)

[Trust Providers](../../user-guide/access-policies/trust-providers/overview.md) attest to workload identities and provide information about the environment in which they operate with high reliability and trustworthiness.

→

![](https://docs.aembit.io/aembit-icons/access-condition.svg)

[Access Conditions](../../user-guide/access-policies/access-conditions/overview.md) are criteria Aembit checks when evaluating an Access Policy to determine whether to grant a Client Workload access to a target Server Workload.

→

![](https://docs.aembit.io/aembit-icons/content-security.svg)

[Content Security](../../user-guide/access-policies/content-security/overview.md) govern the MCP tool traffic that flows through the MCP Identity Gateway.

→

![](https://docs.aembit.io/aembit-icons/credential-provider.svg)

[Credential Providers](../../user-guide/access-policies/credential-providers/overview.md) are systems that provide access credentials, such as OAuth tokens, service account tokens, API keys, or username-and-password pairs.

→

Aembit’s multi-component structure provides many advantages and separates concerns:

* Trust Providers handle identity verification
* Access Conditions handle context
* Content Security Providers handle MCP tool governance
* Credential Providers handle target authentication
* Access Policies orchestrate everything

This modularity allows Aembit to adapt to diverse environments and authentication protocols. See how Aembit evaluates Access Policies in the next section.

## The Access Policy evaluation flow

The power of Aembit Access Policies lies in the coordinated interaction of its distinct components during an access attempt.

The following Access Policy evaluation flow diagram illustrates this process:

![Access Policy evaluation flow from request interception through credential provisioning](https://docs.aembit.io/d2/docs/get-started/concepts/access-policies-0.svg)

The following explains the Access Policy evaluation flow in detail:

1. **Request Initiation & Interception** - A Client Workload attempts to connect to a Server Workload. When you deploy Aembit Edge alongside your Client Workloads, it transparently intercepts this outgoing network request.

2. **Identity Evidence Retrieval** - Aembit Edge interacts with the local environment to retrieve identity evidence suitable for the configured Trust Provider by fetching a cached cloud metadata token or platform OIDC token. Aembit caches identity evidence to prevent Access Policies from failing if the external system goes down for a brief time.

   Upon successful identification, Aembit Cloud identifies the specific Access Policy that governs the interaction between the now-verified Client Workload and the intended Server Workload.

3. **Match request to an Access Policy** - Aembit Edge sends the identity evidence to Aembit Cloud to match the requesting Client Workload and the Server Workload its requesting to access with an Access Policy. If no policy matches both workloads, Aembit denies the request.

4. **Authentication via Trust Provider** - If you’ve configured a Trust Provider, Aembit Cloud uses the appropriate Trust Provider associated with the identified Client Workload to perform cryptographic attestation, verifying the workload’s identity based on its environment. Aembit also caches the identity evidence from the Trust Provider it uses for attestation.

   Aembit logs attestation events to its Authorization Log, which you can view in your Aembit Tenant UI.

5. **Access Condition Check** - If you’ve configured Access Conditions, Aembit Cloud evaluates any Access Conditions associated with the matched Access Policy. This may involve checking time constraints, geographic rules, or querying external systems (like Wiz) for security posture data. If using external systems, Aembit caches their security posture data for the same reasons as for Trust Provider identity evidence.

   The Client Workload must meet all conditions for authorization to proceed.

6. **Credential Provisioning Request** - If Aembit verifies the Client Workload’s identity and it satisfies all Access Conditions, Aembit Cloud logs the Access Policy Authorization Event and then interacts with the Credential Provider.

   Aembit requests an appropriate access credential required by the target Server Workload (like an OAuth token, a temporary AWS key via STS, or an Azure token via WIF).

7. **Credential Injection & Request Forwarding** - Aembit Cloud returns the policy decision (allow) and the freshly obtained access credential to Aembit Edge.

   Finally, Aembit Edge injects the credential into the original Client Workload’s request (like adding an `Authorization: Bearer <token>` header) and forwards the modified request to the actual Server Workload endpoint.

> **Note**
>
> For MCP Identity Gateway policies that include a Content Security Provider, MCP tool traffic also passes through that provider’s evaluation on the request path. See [Content Security](content-security.md).

## Additional resources

* [Aembit Edge: The data plane](aembit-edge.md)
* [Aembit Cloud: The control plane](aembit-cloud.md)
* [Aembit administration](administration.md)
* [Scaling with Terraform](scaling-terraform.md)
