---
type: explanation
title: "Security compliance"
description: "Overview of Aembit's security posture and compliance"
resource: https://docs.aembit.io/get-started/security-posture/security-compliance/
tags: [security-posture]
timestamp: 2026-03-11T16:17:54-07:00
type_inferred: true
---

# Security compliance

Aembit is a cloud-native Workload IAM**Workload IAM**: Identity and Access Management for non-human entities (services, applications, automated workloads) rather than human users. Unlike traditional IAM that manages user access, Workload IAM manages machine-to-machine authentication and authorization.[Learn more](../how-aembit-works.md) platform committed to maintaining the highest standards of security, privacy, and compliance for its customers. The security program meets the needs of organizations with stringent regulatory and risk management requirements.

Looking for deployment architecture?

This page covers Aembit’s security compliance certifications and processes. For deployment architecture, infrastructure components, and integration details, see:

* [How Aembit Works](../how-aembit-works.md) for simplified architecture and components
* [Software Architecture](architecture.md) for detailed deployment zones and data flow

## How Aembit compliance supports your organization

[Section titled “How Aembit compliance supports your organization”](#how-aembit-compliance-supports-your-organization)

Aembit’s SOC 2 Type II**SOC 2 (Service Organization Control 2)**: An auditing standard for service providers that store customer data in the cloud. SOC 2 Type II reports demonstrate the effectiveness of security controls over time across five Trust Service Criteria - Security, Availability, Processing Integrity, Confidentiality, and Privacy.[Learn more(opens in new tab)](https://www.aicpa.org/soc2) and ISO/IEC 27001:2022**ISO/IEC 27001**: An international standard for information security management. Certification demonstrates that an organization has implemented comprehensive security controls and risk management processes aligned with ISO 27001 Annex A requirements.[Learn more(opens in new tab)](https://www.iso.org/isoiec-27001-information-security.html) certifications provide assurance that workload identity and access management operations follow rigorous security controls. These certifications can support your organization’s compliance with:

* **HIPAA Security Rule** (§164.308-312): SOC 2 controls for access management, audit logging, and encryption support healthcare data protection requirements
* **PCI-DSS Requirement 12**: Security policy enforcement and RBAC**RBAC (Role-Based Access Control)**: A security model that restricts system access based on user roles. In Aembit, RBAC controls which administrators can perform specific actions like creating policies, viewing audit logs, or managing credentials.[Learn more](../../user-guide/administration/roles/overview.md) align with PCI requirements for cardholder data environments
* **Sarbanes-Oxley Act (SOX) IT Controls**: SOC 2 audit provides evidence for SOX internal control assessments
* **FedRAMP Authorization**: ISO 27001 framework aligns with NIST 800-53 controls required for federal cloud systems

When you use Aembit to manage workload access:

* **Inherit audit evidence**: SOC 2 and ISO 27001 reports provide third-party validation you can reference in your own audits
* **Reduce compliance scope**: Centralized credential management reduces the number of systems requiring direct compliance assessment
* **Automate evidence collection**: Aembit’s audit logs provide tamper-proof records of access events for compliance reporting

For detailed control mappings and compliance reports, visit the [Aembit Trust Center](https://trust.aembit.io/) or contact <security@aembit.io>.

## Service certifications

[Section titled “Service certifications”](#service-certifications)

Aembit’s Workload IAM platform has achieved the following certifications:

* **SOC 2 Type II**: Demonstrates the effectiveness of security, availability, and confidentiality controls.
* **ISO/IEC 27001:2022**: Validates the information security management system (ISMS)**ISMS (Information Security Management System)**: A systematic approach to managing sensitive information, ensuring confidentiality, integrity, and availability. ISO 27001 certifies that an organization's ISMS meets international standards for risk management and security controls.[Learn more(opens in new tab)](https://www.iso.org/isoiec-27001-information-security.html) and risk management practices.

For the most current certifications and reports, visit the [Aembit Trust Center](https://trust.aembit.io/).

## Compliance processes

[Section titled “Compliance processes”](#compliance-processes)

* **Continuous Monitoring**: Automated tools and manual reviews monitor controls and infrastructure.
* **Regular Audits**: Annual third-party audits for SOC 2 Type II and ISO 27001 validate compliance posture.
* **Penetration Testing**: Independent security firms conduct routine penetration tests.
* **Policy Enforcement**: Global policy compliance settings ensure consistent security requirements across all Access Policies.
* **Role-Based Access Control (RBAC)**: Granular role-based access control protects administrative actions and sensitive operations.

## Shared responsibility model

[Section titled “Shared responsibility model”](#shared-responsibility-model)

Aembit follows a shared responsibility model similar to other SaaS platforms:

### What Aembit secures (managed by Aembit)

[Section titled “What Aembit secures (managed by Aembit)”](#what-aembit-secures-managed-by-aembit)

* **Platform Infrastructure**: Cloud hosting, network security, infrastructure hardening
* **Security Controls**: Continuous monitoring, penetration testing, vulnerability management, audit processes
* **Compliance Programs**: SOC 2, ISO 27001 certifications, annual audits, regulatory updates
* **Service Availability**: High availability, disaster recovery, backup and restoration

Aembit fully manages all preceding compliance processes (monitoring, audits, penetration testing, RBAC for Aembit administration). Your team doesn’t configure or maintain these platform-level controls.

### What you secure (your responsibilities)

[Section titled “What you secure (your responsibilities)”](#what-you-secure-your-responsibilities)

* **Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md)**: Defining which workloads can access which resources and under what conditions
* **Workload Identity Verification**: Configuring Trust Providers and verifying workload identities in your environments
* **Credential Lifecycle**: Managing credential rotation policies and integration with your Credential Providers
* **Integration Security**: Ensuring secure communication (TLS), network access controls, and firewall rules for Edge Components
* **User Access Management**: Managing who can administer Aembit policies and with what permissions

For deployment architecture and operational details, see [Software Architecture](architecture.md).

## Ongoing commitment

[Section titled “Ongoing commitment”](#ongoing-commitment)

Aembit’s security and compliance program continuously evolves to address emerging threats and regulatory changes. Review the [Aembit Trust Center](https://trust.aembit.io/) for the latest updates.

## Exclusions and rationale

[Section titled “Exclusions and rationale”](#exclusions-and-rationale)

This overview excludes internal audit results, detailed control mappings, and proprietary operational procedures. For access to detailed reports (under non-disclosure agreement (NDA)), contact <security@aembit.io>.

Evaluating Aembit for your organization?

**Next steps for your security review:**

1. **Request detailed compliance reports** at [Aembit Trust Center](https://trust.aembit.io/)
2. **Review the security architecture** at [Software Architecture](architecture.md) and [Threat Model](threat-model.md)
3. **Discuss your specific requirements** by contacting <security@aembit.io> for NDA-protected detailed reports and compliance mapping

For general questions, see [What is Aembit?](../overview.md) or start with the [quickstart guides](../quickstart/overview.md).
