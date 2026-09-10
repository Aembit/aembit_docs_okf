---
type: explanation
title: "About Aembit Cloud"
description: "Understanding Aembit Cloud and its role as the central control plane and management plane for workload identity and access management"
resource: https://docs.aembit.io/get-started/concepts/aembit-cloud/
tags: ["concept"]
timestamp: 2026-09-08T23:32:41-07:00
---

# About Aembit Cloud

Aembit Cloud serves as both the central control plane and management plane for the Aembit Workload Identity and Access Management platform. Operating as a Software-as-a-Service (SaaS) offering, Aembit provides the intelligence, decision-making, configuration, and management capabilities. These capabilities govern secure interactions between non-human identities across diverse IT environments.

As the **control plane**, Aembit Cloud makes authorization decisions, evaluates policies, and coordinates credential issuance.

As the **management plane**, it provides the administrative interfaces, configuration management, and operational oversight needed to define policies, manage workloads, and monitor system behavior.

Aembit Cloud functions as the authoritative source for defining and evaluating access policies, managing workload identities, brokering credentials, and providing comprehensive visibility into workload-to-workload communications. It centralizes fragmented access management approaches scattered across multiple clouds, on-premises systems, and SaaS applications.

The platform enables organizations to shift from managing static, long-lived secrets to managing access based on verified workload identities. By acting as an identity broker and policy enforcement coordinator, Aembit Cloud facilitates Zero Trust security principles for non-human interactions. This ensures that Aembit verifies every access request regardless of network location.

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Start using Aembit Cloud](../../user-guide/administration/overview.md)See Administration in the User Guide

→

## How Aembit Cloud works

The following steps outline how Aembit Cloud operates as both the control plane and management plane for workload access management:

1. **Policy Configuration** - Administrators use Aembit Cloud’s management plane capabilities to define access policies through web UI or API, specifying which Client Workloads can access which

   Server Workloads under what conditions.

2. **Identity Verification** - When a workload requests access, Aembit Cloud’s control plane receives attestation data from Aembit Edge components and validates the workload’s identity using configured Trust Providers.

3. **Policy Evaluation** - The control plane’s policy engine evaluates the verified identity against defined access policies, including any Access Conditions such as time constraints, geographic location, or security posture requirements.

4. **Context Assessment** - For conditional access policies, the control plane gathers additional context from integrated security tools or environmental factors to make informed authorization decisions.

5. **Credential Brokering** - If Aembit authorizes access, the control plane invokes the appropriate Credential Provider to obtain or generate the necessary access credentials for the target service.

6. **Decision Transmission** - Aembit Cloud sends the authorization decision and credentials (if approved) back to the requesting Aembit Edge component for enforcement and credential injection.

The following diagram illustrates this control plane and management plane architecture:

![Aembit Cloud control plane and management plane architecture showing policy evaluation, identity verification, and credential brokering flow](https://docs.aembit.io/d2/docs/get-started/concepts/aembit-cloud-0.svg)

## Core capabilities

Aembit Cloud integrates multiple key capabilities across both control plane and management plane functions:

### Control plane capabilities

**Access Policy Engine** - The core decision-making component that evaluates access policies during workload access requests.

**Identity Federation Hub** - Verifies workload identities through attestation and brokers trust between different identity domains.

**Credential Brokering** - Interacts with external credential providers to obtain or generate access credentials just-in-time for authorized workloads.

### Management plane capabilities

**Administrative Interfaces** - Provides web UI, API, and Terraform provider for configuring, monitoring, and managing the entire platform.

**Configuration Management** - Handles the definition, storage, and distribution of policies, workload definitions, and system configurations.

**Workload Directory** - Maintains comprehensive inventory and discovery of Client and Server Workloads across the environment.

**Auditing and Logging** - Captures, stores, and analyzes detailed records of access events, policy evaluations, and administrative changes.

### Integrated capabilities spanning both planes

**Security Integrations** - Connects with external security tools (CrowdStrike, Wiz, etc.) for posture assessment and policy enforcement.

**Identity Provider Management** - Configures and maintains trust relationships with multiple identity providers across cloud and on-premises environments.

**Compliance and Reporting** - Generates compliance reports and provides security monitoring capabilities across both operational and administrative activities.

## Deployment and operational model

### SaaS delivery

Aembit Cloud operates as a **multi-tenant SaaS platform**, providing both control plane and management plane capabilities as a managed service:

* **High availability** through multi-region deployment with automatic failover
* **Scalability** with auto-scaling capabilities to handle millions of workload identities
* **Operational simplicity** by consolidating both control and management functions
* **Continuous updates** and security patches without customer intervention

### Three-plane architecture separation

The architecture separates responsibilities across three distinct planes:

* **Management plane** (Aembit Cloud): Configuration, administration, auditing, monitoring
* **Control plane** (Aembit Cloud): Real-time policy evaluation, identity verification, credential brokering
* **Data plane** (Aembit Edge): Request interception, credential injection, local enforcement

This separation enables **static stability**, where Edge components can continue operating with buffered credentials during temporary Cloud outages, while administrative functions remain centralized for consistency and control.

## Benefits of using Aembit Cloud

* **Unified Control and Management** - Combines access control with comprehensive administrative capabilities in a single platform.

* **Zero Trust Implementation** - Enables continuous verification of workload identities and context for every access request, regardless of network location.

* **Centralized Operations** - Provides single-pane-of-glass management for policies, identities, and access across diverse environments.

* **Secretless Architecture** - Facilitates the shift away from static, long-lived secrets to dynamic, identity-based access management.

* **Comprehensive Visibility** - Delivers integrated auditing and monitoring of both operational access events and administrative changes.

* **Scalable SaaS Delivery** - Leverages cloud-native architecture to handle enterprise-scale workload access management with high availability.

* **Identity Federation Abstraction** - Transforms complex, application-specific identity federation into reusable platform capabilities.

* **Policy Consistency** - Ensures uniform application of access policies across multi-cloud, SaaS, and on-premises environments through centralized management.

* **Operational Resilience** - Maintains service availability through architectural separation and local credential buffering capabilities.

* **Administrative Efficiency** - Streamlines policy management, workload discovery, and compliance reporting through integrated management plane functions.
