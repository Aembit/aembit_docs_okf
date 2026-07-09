---
type: explanation
title: "About Client Workloads"
description: "Understanding Client Workloads and their role as access requesters in Aembit"
resource: https://docs.aembit.io/get-started/concepts/client-workloads/
tags: [concept]
timestamp: 2026-04-13T13:05:24-07:00
type_inferred: true
---

# About Client Workloads

Client Workloads**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](client-workloads.md) represent the software applications, scripts, or automated processes that initiate access requests to Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](server-workloads.md). They’re the “clients” in Aembit’s client-server access model, acting as the requesting party that needs to consume services, APIs, or data from other workloads.

Unlike human users, Client Workloads operate autonomously without direct user interaction. They include applications like microservices, CI/CD pipeline jobs, serverless functions, background scripts, and AI agents that need to access databases, APIs, or other services as part of their automated workflows.

The core challenge Client Workloads solve is **secretless authentication**—eliminating the need to store and manage long-lived credentials like API keys or passwords within the workload itself. Instead, Aembit identifies and authenticates Client Workloads based on verifiable evidence from their runtime environment.

![](https://docs.aembit.io/aembit-icons/client-workload.svg)

[Start configuring Client Workloads ](../../user-guide/access-policies/client-workloads/overview.md)See Client Workloads in the User Guide

→

## How Client Workloads work

[Section titled “How Client Workloads work”](#how-client-workloads-work)

The following steps outline how Client Workloads function within Aembit’s access control flow:

1. **Access Request** - A Client Workload (for example, a microservice, CI/CD job, or Lambda function) attempts to access a Server Workload (for example, a database or API).

2. **Send Identity Evidence** - Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](aembit-edge.md) intercepts the request and collects identity evidence from the Client Workload’s runtime environment. This evidence varies by platform—for example, Kubernetes service account tokens, AWS instance metadata, or GitHub Actions OIDC tokens. Aembit Edge then sends this evidence to Aembit Cloud for processing.

3. **Identity Matching** - Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](aembit-cloud.md) compares the collected evidence against configured Client Workload definitions to identify which specific workload is making the request.

4. **Policy Evaluation** - Once identified, Aembit Cloud locates the appropriate Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](access-policies.md) that links the identified Client Workload to the target Server Workload.

5. **Authentication and Authorization** - The Access Policy’s Trust Providers**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](trust-providers.md) cryptographically verify the Client Workload’s identity, and any Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](access-conditions.md) are evaluated.

6. **Credential Retrieval** - If access passes authorization, Aembit obtains the necessary credentials from the configured Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](credential-providers.md).

   The Credential Provider is specifically associated with the target Server Workload and knows how to generate or retrieve the appropriate authentication credentials (such as API keys, OAuth tokens, or database passwords) that the Server Workload expects.

7. **Credential Injection** - Aembit Edge injects the obtained credentials into the Client Workload’s original request and forwards the modified request to the Server Workload.

The following diagram illustrates this process:

![Client Workload access flow showing how Aembit Edge intercepts requests, verifies identity through Aembit Cloud, and injects credentials](https://docs.aembit.io/d2/docs/get-started/concepts/client-workloads-0.svg)

## Supported identification methods

[Section titled “Supported identification methods”](#supported-identification-methods)

Aembit offers multiple identification methods tailored to different deployment environments, called [Client Workload Identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md). These enable you to accurately recognize Client Workloads based on their runtime context and platform-specific attributes.

**Cloud Platforms**

* [AWS identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#aws-client-workload-identifiers) - EC2 Instance ID, ECS Task Family, Lambda ARN, IAM Role ARN, Account ID, and Region
* [Azure identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#azure-client-workload-identifiers) - Subscription ID and VM ID
* [Google Cloud identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#gcp-client-workload-identifiers) - Identity Token claims

**Container Orchestration**

* [Kubernetes identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#kubernetes-client-workload-identifiers) - Pod Name, Pod Name Prefix, Service Account Name, and Namespace

**CI/CD Platforms**

* [GitHub Actions identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#github-client-workload-identifiers) - Repository and Subject claims from OIDC tokens
* [GitLab Jobs identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#gitlab-client-workload-identifiers) - Namespace Path, Project Path, Ref Path, and Subject claims from OIDC tokens
* [Terraform Cloud identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#terraform-cloud) - Organization ID, Project ID, and Workspace ID from OIDC tokens

**Virtual Machines and Generic**

* [Hostname and Process identifiers](../../user-guide/access-policies/client-workloads/identification/overview.md#generic-client-workload-identifiers) - System hostname, process name, process user, and source IP
* [Aembit Client ID](../../user-guide/access-policies/client-workloads/identification/overview.md#generic-client-workload-identifiers) - Native Aembit identifier for edge cases

Aembit supports [configuring multiple identifiers](../../user-guide/access-policies/client-workloads/identification/client-workload-multiple-ids.md) for a single Client Workload definition to increase specificity and prevent misidentification.

## Benefits of using Client Workloads

[Section titled “Benefits of using Client Workloads”](#benefits-of-using-client-workloads)

* **Secretless Authentication** - Eliminates the need for Client Workloads to store or manage long-lived identity secrets like API keys or passwords.
* **Environment-Native Identity** - Leverages existing platform identity mechanisms (Kubernetes service accounts, cloud metadata, OIDC tokens) rather than introducing new credential management overhead.
* **Precise Access Control** - Enables granular policies that specify exactly which workloads can access which resources, supporting the principle of least privilege.
* **Automated Credential Management** - Handles the entire credential lifecycle automatically, from identity verification to credential injection, reducing operational burden.
* **Audit and Compliance** - Provides detailed logging of which workloads accessed what resources and when, supporting security monitoring and compliance requirements.
