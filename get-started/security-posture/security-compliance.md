---
type: explanation
title: "Security compliance"
description: "Overview of Aembit's security posture and compliance"
resource: https://docs.aembit.io/get-started/security-posture/security-compliance/
tags: ["security-posture"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Security compliance

Aembit is a cloud-native Workload IAM platform committed to maintaining the highest standards of security, privacy, and compliance for its customers. The security program meets the needs of organizations with stringent regulatory and risk management requirements.

> **Looking for deployment architecture?**
>
> This page covers Aembit’s security compliance certifications and processes. For deployment architecture, infrastructure components, and integration details, see:
>
> * [How Aembit Works](../how-aembit-works.md) for simplified architecture and components
> * [Software Architecture](architecture.md) for detailed deployment zones and data flow

## How Aembit compliance supports your organization

Aembit’s SOC 2 Type II and

ISO/IEC 27001:2022 certifications provide assurance that workload identity and access management operations follow rigorous security controls. These certifications can support your organization’s compliance with:

* **HIPAA Security Rule** (§164.308-312): SOC 2 controls for access management, audit logging, and encryption support healthcare data protection requirements
* **PCI-DSS Requirement 12**: Security policy enforcement and RBAC align with PCI requirements for cardholder data environments
* **Sarbanes-Oxley Act (SOX) IT Controls**: SOC 2 audit provides evidence for SOX internal control assessments
* **FedRAMP Authorization**: ISO 27001 framework aligns with NIST 800-53 controls required for federal cloud systems

When you use Aembit to manage workload access:

* **Inherit audit evidence**: SOC 2 and ISO 27001 reports provide third-party validation you can reference in your own audits
* **Reduce compliance scope**: Centralized credential management reduces the number of systems requiring direct compliance assessment
* **Automate evidence collection**: Aembit’s audit logs provide tamper-proof records of access events for compliance reporting

For detailed control mappings and compliance reports, visit the [Aembit Trust Center](https://trust.aembit.io/) or contact <security@aembit.io>.

## Service certifications

Aembit’s Workload IAM platform has achieved the following certifications:

* **SOC 2 Type II**: Demonstrates the effectiveness of security, availability, and confidentiality controls.
* **ISO/IEC 27001:2022**: Validates the information security management system (ISMS) and risk management practices.

For the most current certifications and reports, visit the [Aembit Trust Center](https://trust.aembit.io/).

## Compliance processes

* **Continuous Monitoring**: Automated tools and manual reviews monitor controls and infrastructure.
* **Regular Audits**: Annual third-party audits for SOC 2 Type II and ISO 27001 validate compliance posture.
* **Penetration Testing**: Independent security firms conduct routine penetration tests.
* **Policy Enforcement**: Global policy compliance settings ensure consistent security requirements across all Access Policies.
* **Role-Based Access Control (RBAC)**: Granular role-based access control protects administrative actions and sensitive operations.

## Shared responsibility model

Aembit follows a shared responsibility model similar to other SaaS platforms:

### What Aembit secures (managed by Aembit)

* **Platform Infrastructure**: Cloud hosting, network security, infrastructure hardening
* **Security Controls**: Continuous monitoring, penetration testing, vulnerability management, audit processes
* **Compliance Programs**: SOC 2, ISO 27001 certifications, annual audits, regulatory updates
* **Service Availability**: High availability, disaster recovery, backup and restoration

Aembit fully manages all preceding compliance processes (monitoring, audits, penetration testing, RBAC for Aembit administration). Your team doesn’t configure or maintain these platform-level controls.

### What you secure (your responsibilities)

* **Access Policies**: Defining which workloads can access which resources and under what conditions
* **Workload Identity Verification**: Configuring Trust Providers and verifying workload identities in your environments
* **Credential Lifecycle**: Managing credential rotation policies and integration with your Credential Providers
* **Integration Security**: Ensuring secure communication (TLS), network access controls, and firewall rules for Edge Components
* **User Access Management**: Managing who can administer Aembit policies and with what permissions

For deployment architecture and operational details, see [Software Architecture](architecture.md).

## Ongoing commitment

Aembit’s security and compliance program continuously evolves to address emerging threats and regulatory changes. Review the [Aembit Trust Center](https://trust.aembit.io/) for the latest updates.

## Exclusions and rationale

This overview excludes internal audit results, detailed control mappings, and proprietary operational procedures. For access to detailed reports (under non-disclosure agreement (NDA)), contact <security@aembit.io>.

> **Evaluating Aembit for your organization?**
>
> **Next steps for your security review:**
>
> 1. **Request detailed compliance reports** at [Aembit Trust Center](https://trust.aembit.io/)
> 2. **Review the security architecture** at [Software Architecture](architecture.md) and [Threat Model](threat-model.md)
> 3. **Discuss your specific requirements** by contacting <security@aembit.io> for NDA-protected detailed reports and compliance mapping
>
> For general questions, see [What is Aembit?](../overview.md) or start with the [quickstart guides](../quickstart/overview.md).
