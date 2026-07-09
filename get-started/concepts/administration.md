---
type: explanation
title: "About Administering Aembit"
description: "Discover Aembit's administration capabilities"
resource: https://docs.aembit.io/get-started/concepts/administration/
tags: [concept]
timestamp: 2025-10-21T20:11:12-07:00
type_inferred: true
---

# About Administering Aembit

This page provides an of all administrative capabilities available in your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](administration.md).

## Admin dashboard

[Section titled “Admin dashboard”](#admin-dashboard)

The Admin dashboard serves as your command center for monitoring the health and activity of your Aembit deployment. It provides real-time visibility into workload connections, credential usage, and potential security issues. This visibility allows you to identify and address operational concerns.

The [Admin dashboard](../../user-guide/administration/admin-dashboard/overview.md) provides:

* Summary metrics for configured workloads and entities

* Workload event history with severity indicators

* Client and Server Workloads connection metrics

* Credential usage analytics

* Application protocol distribution

* Access condition failure monitoring

## User management

[Section titled “User management”](#user-management)

User management in Aembit allows you to control who can access your Aembit Tenant and what actions they can perform. This capability is essential for implementing the principle of least privilege and making sure you have proper separation of duties within your organization.

[User management](../../user-guide/administration/users/overview.md) features include:

* [Add users](../../user-guide/administration/users/add-user.md) with specific roles and contact information

* Configure external authentication options

* Manage user credentials and access rights

## Roles and permissions

[Section titled “Roles and permissions”](#roles-and-permissions)

Aembit’s role-based access control system allows you to create customized roles with precise permissions. This enables you to delegate administrative responsibilities without granting excessive privileges. This granular approach to access control helps maintain security while supporting collaborative administration.

[Role-based access control](../../user-guide/administration/roles/overview.md) provides:

* [Create specialized roles](../../user-guide/administration/roles/add-roles.md) beyond default SuperAdmin and Auditor

* Configure granular permissions for each role

* Integrate with Resource Sets for multi-tenancy

## Workload Discovery

[Section titled “Workload Discovery”](#workload-discovery)

Workload Discovery automates the identification and management of workloads within your Aembit environment. It simplifies the process of adding new workloads by automatically detecting them to provide a streamlined workflow for onboarding.

Workload Discovery allows you to:

* [Manage Workload Discovery](../../user-guide/administration/discovery/overview.md) in your environment.
* Integrate security tools like [Wiz](../../user-guide/administration/discovery/integrations/wiz.md) to discover workloads.

## Identity providers

[Section titled “Identity providers”](#identity-providers)

Identity provider integration allows you to leverage your existing identity infrastructure with Aembit. By connecting your corporate identity provider, you can make sure consistent authentication policies across your organization. This integration simplifies user management through automatic provisioning and role mapping.

[Identity provider integration](../../user-guide/administration/identity-providers/overview.md) enables:

* Connect with [SAML 2.0 providers](../../user-guide/administration/identity-providers/create-idp-saml.md) (Okta, Google, Microsoft Entra ID)

* Enable Single Sign-On (SSO) authentication

* Configure [SSO automatic user creation](../../user-guide/administration/identity-providers/automatic-user-creation.md) for new users

## Resource Sets

[Section titled “Resource Sets”](#resource-sets)

Resource Sets provide powerful multi-tenancy capabilities, allowing you to segment your Aembit environment for different teams, applications, or business units. This isolation makes sure administrators can only manage resources within their assigned domains. It supports organizational boundaries while maintaining centralized oversight.

[Resource Sets](../../user-guide/administration/resource-sets/overview.md) allow you to:

* [Create isolated resource groups](../../user-guide/administration/resource-sets/create-resource-set.md)

* [Add workloads and resources](../../user-guide/administration/resource-sets/adding-resources-to-resource-set.md) to specific sets

* [Assign roles](../../user-guide/administration/resource-sets/assign-roles.md) for managing each Resource Set

* [Deploy Resource Sets](../../user-guide/administration/resource-sets/deploy-resource-set.md) using specific methods

## Global Policy Compliance

[Section titled “Global Policy Compliance”](#global-policy-compliance)

Aembit’s Global Policy Compliance is a security enforcement feature that allows you to establish organization-wide security standards for Access Policies and Agent Controllers. Global Policy Compliance ensures consistent security practices across your Aembit environment and prevents the creation of policies that might inadvertently expose resources.

See [Global Policy Compliance](../../user-guide/administration/global-policy/overview.md) for more information and configuration details, and see [Global Policy Compliance report dashboard](../../user-guide/audit-report/global-policy.md) to review the compliance status of your Aembit Tenant’s global policies.

## Log streams

[Section titled “Log streams”](#log-streams)

Log streams extend Aembit’s audit and monitoring capabilities by forwarding logs to external systems. This enables long-term storage, analysis, and compliance reporting. The integration with your existing security monitoring infrastructure allows Aembit activity to become part of your organization’s overall security operations.

[Log streams](../../user-guide/administration/log-streams/overview.md) allow you to:

* Forward logs to [AWS S3 buckets](../../user-guide/administration/log-streams/aws-s3.md)

* Export logs to [Google Cloud Storage](../../user-guide/administration/log-streams/gcs-bucket.md)

* Configure multiple stream types for different log categories

## Sign-on policy

[Section titled “Sign-on policy”](#sign-on-policy)

Sign-on policy controls how administrators authenticate to the Aembit platform. This central configuration point allows you to enforce strong authentication requirements. It makes sure that access to this privileged system follows your organization’s security standards.

The [Sign-on policy](../../user-guide/administration/sign-on-policy/overview.md) page allows you to:

* Configure SSO enforcement requirements

* Set up multi-factor authentication policies

* Manage authentication grace periods
