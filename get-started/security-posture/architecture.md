---
type: explanation
title: "Aembit software architecture"
description: "Explanation and illustration of Aembit's software architecture"
resource: https://docs.aembit.io/get-started/security-posture/architecture/
tags: [security-posture]
timestamp: 2026-06-26T15:17:49-07:00
type_inferred: true
---

# Aembit software architecture

Aembit is a cloud-native, multi-tenant SaaS Workload IAM**Workload IAM**: Identity and Access Management for non-human entities (services, applications, automated workloads) rather than human users. Unlike traditional IAM that manages user access, Workload IAM manages machine-to-machine authentication and authorization.[Learn more](../how-aembit-works.md) platform for federated workloads that provides credential brokering**Credential brokering**: The process of mediating credential issuance between a requesting workload and a target resource. The broker verifies identity, evaluates policy, and issues short-lived credentials so that workloads never manage static secrets directly.[Learn more](../how-aembit-works.md) and access policy enforcement across diverse infrastructure environments.

## High-level architecture overview

[Section titled “High-level architecture overview”](#high-level-architecture-overview)

Aembit’s architecture consists of two primary systems:

* **Aembit Cloud**: A multi-tenant SaaS platform responsible for centralized management, policy enforcement, credential brokering, and audit logging.
* **Aembit Edge**: Lightweight components deployed in customer environments (Kubernetes clusters, VMs, CI/CD pipelines) that enforce access policies and broker credentials locally.

**Deployment:** Edge components deploy as containers (Kubernetes), VMs (Linux/Windows), or serverless functions (AWS Lambda/ECS Fargate). They require outbound HTTPS connectivity to Aembit Cloud (no inbound ports) and you can deploy them redundantly for high availability. Edge components are stateless, enabling flexible scaling. For detailed deployment options, see [Deploy Aembit Edge](../../user-guide/deploy-install/overview.md).

### Key components

[Section titled “Key components”](#key-components)

* **Workloads**: Applications or services (clients and servers) that require access to resources.
* **Access Policies**: Declarative rules that define which workloads can access which resources, under what conditions.
* **Trust Providers**: Integrations with identity providers (AWS, Azure, Google Cloud) to verify workload identities.
* **Credential Providers**: Integrations that securely issue short-lived credentials (cloud IAM tokens, database passwords) to workloads.
* **Audit logs and workload events**: Comprehensive logging of access requests, policy evaluations, and credential issuance for compliance and forensics.

## Architecture diagram

[Section titled “Architecture diagram”](#architecture-diagram)

Below is a high-level D2 diagram representing Aembit’s architecture:

![High-level architecture showing Client Workloads requesting access through Aembit Edge, policy evaluation in Aembit Cloud, identity verification via Trust Providers, and credential issuance to access Server Workloads](https://docs.aembit.io/d2/docs/get-started/security-posture/architecture-0.svg)

Aembit’s deployment model shows three zones: Aembit Cloud (SaaS, managed by Aembit), Your Infrastructure (where you deploy Edge components), and External Systems (existing identity providers and target workloads). Numbered arrows show a typical access flow from workload request through policy evaluation to credential delivery.

## Security design principles

[Section titled “Security design principles”](#security-design-principles)

Aembit’s architecture follows modern security principles:

* **Zero Trust**: Aembit authenticates every access request via workload identity and authorizes it against policy, with no implicit trust based on network location. This prevents lateral movement even if an attacker gains network access.

  Technical example

  Even if a container runs inside your trusted VPC, Aembit Edge requires identity attestation (via AWS IAM Role, Kubernetes Service Account, etc.) and policy evaluation before granting credentials. Network location alone is never sufficient. A compromised pod in your Kubernetes cluster can’t access production databases without valid service account credentials and an Access Policy authorizing that specific workload.

* **Least Privilege**: Aembit issues credentials dynamically for specific access requests only, with minimal scope and short validity periods. No long-lived static credentials exist in workload configurations or environment variables.

  Technical example

  When your application requests database access, Aembit issues credentials scoped to only that database. Each credential has a configurable TTL**TTL (Time to Live)**: The configured duration for which a credential, token, or cached value remains valid before expiring. Shorter TTLs reduce the window of exposure if a credential is compromised.. The credential never exists in your container’s environment variables or configuration files. It’s delivered just-in-time via the Aembit Edge proxy. If an attacker dumps your container’s memory or environment, they won’t find static database passwords.

* **Defense in Depth**: Multiple security layers protect credential access: identity attestation at Edge, policy evaluation in Cloud, credential generation by providers, and audit logging at all stages. Compromise of any single layer doesn’t expose credentials.

  Technical example

  To access AWS S3, a workload must first pass identity verification at Edge (a valid Kubernetes service account token). It must then match an Access Policy in Cloud (including any time-based or IP-based conditions) and receive temporary AWS STS credentials from the Credential Provider. An attacker who compromises only the Edge component can’t forge credentials without also controlling the Cloud policy engine and STS credential generation.

* **Separation of Duties**: Separate components with distinct responsibilities handle identity verification (Trust Providers), policy decisions (Aembit Cloud), and credential issuance (Credential Providers). No single system has full control over access decisions.

  Technical example

  AWS validates workload identity via IAM roles (Trust Provider), Aembit Cloud evaluates Access Policies based on that identity, and then AWS STS issues temporary credentials (Credential Provider). No single component can unilaterally grant access. The Trust Provider can’t issue credentials, and the Credential Provider can’t override policy decisions. This separation limits the blast radius of any individual component compromise.

## Next steps

[Section titled “Next steps”](#next-steps)

**To validate how this architecture addresses specific security scenarios:**

* [Threat Model](threat-model.md) for how the architecture prevents common attacks
* [Use Cases](../use-cases/overview.md) for practical security scenarios

**To plan your deployment:**

* [Deploy Aembit Edge](../../user-guide/deploy-install/overview.md) for deployment overview and planning
* [Kubernetes Deployment](../../user-guide/deploy-install/kubernetes/kubernetes.md) for Kubernetes clusters
* [VM Deployment](../../user-guide/deploy-install/virtual-machine/overview.md) for Linux/Windows VMs
* [Serverless Deployment](../../user-guide/deploy-install/serverless/overview.md) for Lambda/ECS Fargate
* [CI/CD Deployment](../../user-guide/deploy-install/ci-cd/overview.md) for GitHub Actions and GitLab

**To understand component details:**

* [Aembit Edge Concepts](../concepts/aembit-edge.md) for Edge component details
* [Aembit Cloud Concepts](../concepts/aembit-cloud.md) for Cloud architecture details
* [How Aembit Works](../how-aembit-works.md) for end-to-end system flow

**To explore conceptual foundations:**

* [Conceptual Overview](../concepts/overview.md) for core concepts and terminology
* [Access Policies](../concepts/access-policies.md) for policy evaluation flow
* [Trust Providers](../concepts/trust-providers.md) for identity verification concepts
