---
type: explanation
title: "Aembit in your threat model"
description: "How and where Aembit fits into your threat model"
resource: https://docs.aembit.io/get-started/security-posture/threat-model/
tags: [security-posture]
timestamp: 2026-03-12T14:33:26-07:00
type_inferred: true
---

# Aembit in your threat model

New to Aembit?

This page explains Aembit’s threat model for security professionals evaluating workload identity and access management solutions.

If you’re looking for a basic overview of what Aembit does, start with:

* [What is Aembit?](../overview.md) - High-level introduction
* [How Aembit Works](../how-aembit-works.md) - Simplified architecture explanation
* [Core Concepts](../concepts/overview.md) - Foundational terminology and components

Aembit addresses the unique security challenges of managing access between workloads in modern, distributed environments. This section explains the threat modeling approach, the types of threats considered, and how Aembit’s controls mitigate those threats.

## Deployment model

[Section titled “Deployment model”](#deployment-model)

Aembit uses a hybrid deployment model that balances centralized control with distributed enforcement:

* **Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](../concepts/aembit-cloud.md):** Managed SaaS control plane operated by Aembit for policy management, authorization decisions, and audit logging.

* **Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../concepts/aembit-edge.md):** Policy enforcement components deployed in your infrastructure as containers, sidecars, or proxies, depending on your integration pattern.

This model provides centralized policy management while keeping credential brokering close to your workloads for performance and security.

For detailed deployment patterns and architecture, see [Software Architecture](architecture.md).

## Threat modeling methodology

[Section titled “Threat modeling methodology”](#threat-modeling-methodology)

Aembit uses a structured, risk-based threat modeling process that systematically identifies, categorizes, and prioritizes potential security threats. This process follows these steps:

* **Identify Assets**: Workloads, credentials, policies, audit logs.
* **Enumerate Threats**: Unauthorized access, credential theft, supply chain attacks, insider threats, data exfiltration.
* **Assess Risks**: Evaluate likelihood and impact.
* **Define Mitigations**: Implement controls and monitoring.

## Key threat categories and mitigations

[Section titled “Key threat categories and mitigations”](#key-threat-categories-and-mitigations)

| Threat Category      | Example Scenario                                | Aembit Mitigation                                     |
| -------------------- | ----------------------------------------------- | ----------------------------------------------------- |
| Unauthorized Access  | Compromised workload attempts access            | Policy-based access control, identity verification    |
| Credential Theft     | Stolen API keys or tokens                       | Short-lived credentials, secure brokering, audit logs |
| Supply Chain Attacks | Malicious code in dependencies                  | Signed container images, policy enforcement           |
| Insider Threats      | Privileged user abuses access                   | RBAC, audit logging, separation of duties             |
| Data Exfiltration    | Sensitive data sent to unauthorized destination | Policy restrictions, monitoring, alerting             |

## How Aembit fits into your threat model

[Section titled “How Aembit fits into your threat model”](#how-aembit-fits-into-your-threat-model)

Aembit acts as a control point for workload-to-resource access, providing:

* **Centralized Policy Enforcement**: Centrally managed, auditable policies govern all access.
* **Identity Federation**: Integrates with cloud and on-prem identity providers to verify workload identities.
* **Credential Brokering**: Issues ephemeral credentials**Ephemeral credentials**: Short-lived credentials issued on demand that automatically expire after a configured time period. Because they aren't stored persistently, they reduce the risk window if compromised., reducing the risk of long-lived secrets.
* **Comprehensive Auditing**: Aembit logs all access requests and policy decisions for compliance and forensics.

### Threat surfaces and controls

[Section titled “Threat surfaces and controls”](#threat-surfaces-and-controls)

The following diagram illustrates how Aembit mediates all workload access, enforcing policy, and logging events. Aembit denies and logs unauthorized attempts.

![Threat model overview showing how Aembit mediates workload access, enforces policy, logs events, and denies unauthorized attempts from attackers](https://docs.aembit.io/d2/docs/get-started/security-posture/threat-model-0.svg)

### Component definitions

[Section titled “Component definitions”](#component-definitions)

The preceding diagram shows how Aembit components interact:

* **Aembit Cloud** - Managed SaaS control plane for policy management, credential brokering, and audit logging. Operated by Aembit.

* **Aembit Edge** - Policy enforcement components deployed in your environment (as containers, sidecars, or proxies). Managed by you.

* **Protected Resource**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../concepts/server-workloads.md)** - Your databases, APIs, or services requiring access control.

* **Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../concepts/client-workloads.md)** - Your applications or services requesting access to protected resources.

* **Audit Logs** - Centralized event logs stored in Aembit Cloud for compliance and monitoring.

## Security implementation overview

[Section titled “Security implementation overview”](#security-implementation-overview)

### Authentication and authorization

[Section titled “Authentication and authorization”](#authentication-and-authorization)

* **Policy Model:** Policy-based access control with Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md)
* **Identity Verification:** OAuth 2.0/OIDC protocols
* **Policy Enforcement:** Distributed enforcement via Aembit Edge

### Credential management

[Section titled “Credential management”](#credential-management)

* **Ephemeral Credentials:** Short-lived credentials with configurable TTL**TTL (Time to Live)**: The configured duration for which a credential, token, or cached value remains valid before expiring. Shorter TTLs reduce the window of exposure if a credential is compromised.
* **Just-in-Time Issuance:** Credentials issued only when needed, never stored in application code
* **Automatic Rotation:** Supported for compatible Credential Providers**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../concepts/credential-providers.md)

### Encryption and data protection

[Section titled “Encryption and data protection”](#encryption-and-data-protection)

* **Data in Transit:** TLS 1.2 or higher for all communications
* **Data at Rest:** AES-256 encryption for stored data
* **Key Management:** Centralized key management in Aembit Cloud

### Audit and monitoring

[Section titled “Audit and monitoring”](#audit-and-monitoring)

* **Centralized Logging:** All access requests logged to Aembit Cloud
* **SIEM Integration:** Export logs to external SIEM systems
* **Compliance Support:** Audit trail for regulatory requirements

### Detailed security information

[Section titled “Detailed security information”](#detailed-security-information)

For detailed cryptographic specifications, key management procedures, enterprise security commitments, incident response processes, and SLA details, [contact Aembit support](https://docs.aembit.io/support-overview).

## Customer responsibilities

[Section titled “Customer responsibilities”](#customer-responsibilities)

While Aembit provides robust controls, customers are responsible for:

* Securing their own workloads and environments.
* Configuring access policies appropriately.
* Monitoring audit logs and responding to alerts.

## Threat model scope

[Section titled “Threat model scope”](#threat-model-scope)

### In scope: threats Aembit addresses

[Section titled “In scope: threats Aembit addresses”](#in-scope-threats-aembit-addresses)

Aembit’s threat model focuses on workload-to-resource access and identity:

* **Credential Exposure and Theft** - Stolen API keys, leaked secrets, compromised credentials
* **Policy Enforcement** - Unauthorized access, privilege escalation, access policy violations
* **Identity Verification** - Workload impersonation, spoofing, man-in-the-middle attacks
* **Access Auditing** - Visibility into access patterns, compliance monitoring, forensics

### Out of scope: complementary controls

[Section titled “Out of scope: complementary controls”](#out-of-scope-complementary-controls)

Aembit addresses workload identity and access control. You remain responsible for:

* **Network Security** - DDoS protection, packet filtering, network segmentation
* **Application Security** - SQL injection, XSS, input validation, secure coding practices
* **Endpoint Security** - Host hardening, malware protection, patch management
* **Physical Security** - Data center security, hardware protection

### Additional security information

[Section titled “Additional security information”](#additional-security-information)

For Aembit’s compliance certifications and framework mappings, see [Security Compliance](security-compliance.md).

For operational security commitments, SLA details, incident response procedures, and enterprise security specifications, [contact Aembit support](https://docs.aembit.io/support-overview).
