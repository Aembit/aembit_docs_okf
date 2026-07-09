---
type: explanation
title: "About Trust Providers"
description: "Understanding Trust Providers and their role in verifying workload identities in Aembit"
resource: https://docs.aembit.io/get-started/concepts/trust-providers/
tags: [concept]
timestamp: 2026-05-18T14:17:19-07:00
type_inferred: true
---

# About Trust Providers

Trust Providers**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](trust-providers.md) validate the identity of [Client Workloads](client-workloads.md) through a process called workload attestation**Workload Attestation**: Workload attestation cryptographically verifies a workload's identity using evidence from its runtime environment, such as platform identity documents or tokens, rather than using static credentials.[Learn more](trust-providers.md). Instead of relying on pre-shared secrets like API keys, passwords, or certificates Trust Providers verify identity by consulting trusted systems in the workload’s runtime environment.

The core idea is simple but powerful: rather than asking, “What secret do you know?”, Trust Providers ask, “Can your environment vouch for who you are?” It’s similar to checking someone’s government-issued ID rather than taking their word for it.

You can think of Trust Providers as a kind of certificate authority for workloads—but instead of issuing certificates, they produce cryptographically verifiable claims about a workload’s environment. Aembit uses these claims to establish trust before granting access, reducing the risk of unauthorized workloads posing as trusted ones.

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Start configuring Trust Providers ](../../user-guide/access-policies/trust-providers/overview.md)See Trust Providers in the User Guide

→

## How Trust Providers work

[Section titled “How Trust Providers work”](#how-trust-providers-work)

The following steps outline the process of how Trust Providers work in Aembit:

1. **Client Workload Request** - A Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](client-workloads.md) (for example, a microservice or application) attempts to access a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](server-workloads.md) (for example, a database or API).

2. **Workload Attestation** - When a Client Workload attempts to access a Server Workload, Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](aembit-edge.md) gathers identity evidence from the Client Workload’s runtime environment.

3. **Evidence Submission** - Aembit Edge submits this identity evidence to Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](aembit-cloud.md).

4. **Trust Provider Validation** - Aembit Cloud uses a configured Trust Provider to validate the submitted evidence. The Trust Provider checks the evidence against its own records and policies to confirm the workload’s identity.

   Trust Providers vs. Attestation Sources

   When configuring a Trust Provider in Aembit, you aren’t configuring the external attestation source itself (like AWS, Azure, Kubernetes, or GitHub). Instead, you’re telling Aembit how to validate the identity evidence coming from that attestation source and what criteria to use when determining if Aembit should trust a workload.

5. **Identity Confirmation** - If the Trust Provider validates the evidence, Aembit Cloud confirms the Client Workload’s identity.

6. **Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](access-policies.md) Evaluation** - With the workload’s identity established, Aembit Cloud proceeds with evaluating the remaining components of the Access Policy.

   At this point in the process, Aembit continues to evaluate the Access Policy, which may include additional Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](access-conditions.md), such as checking the workload’s attributes, permissions, or other contextual information.

The following diagram illustrates this process:

![Diagram showing how Trust Providers validate Client Workload identity through attestation sources and Aembit Cloud](https://docs.aembit.io/d2/docs/get-started/concepts/trust-providers-0.svg)

## Supported environments

[Section titled “Supported environments”](#supported-environments)

Aembit integrates with a variety of Trust Providers to support workload attestation across different environments, including:

**Cloud Providers**

* [AWS Role](../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) and [AWS Metadata Service](../../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)
* [Azure Instance Metadata Service](../../user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider.md)
* [Google Cloud Platform Identity Token](../../user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md)

**Container Orchestration**

* [Kubernetes Service Account](../../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)

**CI/CD Platforms**

* [GitHub Actions](../../user-guide/access-policies/trust-providers/github-trust-provider.md)
* [GitLab Jobs](../../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)
* [Terraform Cloud Identity Token](../../user-guide/access-policies/trust-providers/terraform-cloud-identity-token-trust-provider.md)

**On-Premises**

* [Kerberos](../../user-guide/access-policies/trust-providers/kerberos-trust-provider.md)

## Benefits of using trust providers

[Section titled “Benefits of using trust providers”](#benefits-of-using-trust-providers)

* **Enhanced Security** - Eliminates reliance on static, long-lived secrets, reducing the attack surface.
* **Simplified Management** - Centralizes identity verification, simplifying access control across diverse environments.
* **Improved Auditability** - Provides a clear audit trail of workload identities and access attempts.
* **Zero-Trust Architecture** - This approach verifies every workload access request before granting access, enabling a zero-trust model.
