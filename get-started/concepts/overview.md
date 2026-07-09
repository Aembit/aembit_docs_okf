---
type: explanation
title: "Conceptual overview"
description: "This page provides a high-level conceptual overview of Aembit and its components"
resource: https://docs.aembit.io/get-started/concepts/
tags: [concept]
timestamp: 2026-02-24T09:31:09-08:00
type_inferred: true
---

# Conceptual overview

This topic explains how Aembit operates behind the scenes (at a high level) to provide secure, seamless access between workloads. Use the links in each section to dive deeper into specific topics related to how Aembit works or start configuring and using those features.

## Aembit as an identity broker

[Section titled “Aembit as an identity broker”](#aembit-as-an-identity-broker)

Aembit operates conceptually as an identity broker. It acts as an intermediary, facilitating secure access requests initiated by a Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](client-workloads.md) (like an application or script) attempting to connect to a target Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](server-workloads.md) (like an API or database).

These workloads may operate across security boundaries or reside in different compute environments. For example, a Client Workload in AWS accessing a Server Workload in Azure. By centralizing the brokering function, Aembit helps you simplify the management of trust relationships and Access Policies across your disparate security boundaries and environments.

## Workloads

[Section titled “Workloads”](#workloads)

Workloads are the fundamental entities in Aembit’s access control model. They represent software applications, services, or processes that either request access to resources ([Client Workloads](#client-workloads)) or provide resources that others access ([Server Workloads](#server-workloads)).

Aembit establishes secure communication channels between these workloads by verifying their identities, evaluating access policies, and providing Just-In-Time (JIT) credentials without requiring code changes to your applications.

### Client Workloads

[Section titled “Client Workloads”](#client-workloads)

Client Workloads are the initiators of access requests in Aembit’s security model. They represent any non-human entity that needs to consume services or resources provided by Server Workloads. Examples include:

* Web applications requesting data from APIs
* Microservices communicating with other services
* Background jobs accessing databases
* CI/CD pipelines deploying to cloud environments
* Scheduled tasks retrieving configuration information

When a Client Workload attempts to access a Server Workload, [Aembit Edge](#aembit-edge) intercepts the request and works with [Aembit Cloud](#aembit-cloud) to verify the Client Workload’s identity through an [Access Policy](#access-policies). This verification happens without the Client Workload storing or managing long-lived credentials, eliminating credential sprawl, and reducing security risks.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Client Workloads ](client-workloads.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Client Workloads ](../../user-guide/access-policies/client-workloads/overview.md)See the Aembit User Guide

→

### Server Workloads

[Section titled “Server Workloads”](#server-workloads)

Server Workloads are the targets of access requests in Aembit’s security model. They represent services or resources that Client Workloads need to access. Examples include:

* REST APIs and web services
* Databases and data warehouses
* Third-party SaaS applications
* Cloud provider services
* Legacy applications and internal systems

Server Workloads can exist in multiple environments, like public cloud, private cloud, on-premises, or SaaS, and Aembit provides consistent access controls regardless of their location. For each Server Workload, you can define authentication requirements, network locations, and specific access restrictions.

Aembit helps you manage credentials for Server Workloads through Credential Providers, which generate or retrieve the appropriate authentication material for each Server Workload once Aembit grants access.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Server Workloads ](server-workloads.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Server Workloads ](../../user-guide/access-policies/server-workloads/overview.md)See the Aembit User Guide

→

***

## Access Policies

[Section titled “Access Policies”](#access-policies)

Aembit uses Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](access-policies.md) to control which Client Workloads can access which Server Workloads and under what conditions.

Access Policies evaluate the following components when making access decisions:

* **Client Workloads** - Any non-human entity that initiates an access request to consume a service or resource provided by a Server Workload.
* Trust Providers**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](trust-providers.md) - Attest to workload identities and provide information about the environment in which they operate with high reliability and trustworthiness.
* Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](access-conditions.md) - Criteria Aembit checks when evaluating an Access Policy to determine whether to grant a Client Workload access to a target Server Workload.
* Credential Providers**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](credential-providers.md) - Systems that provide access credentials, such as OAuth tokens, service account tokens, API keys, or username-and-password pairs.
* **Server Workloads** - Software applications that serve requests from Client Workloads such as third-party SaaS APIs, API gateways, databases, and data warehouses.

For a simplified illustration of the Access Policy evaluation flow, see \[Evaluation flow: how Aembit grants access]\(/get-started/how-aembit-works#access-policy-flow-putting-it-all together).

If a request meets all requirements, Aembit allows the connection and injects the credential. If any step fails, Aembit denies the request and logs the reason.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Access Policies ](access-policies.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Access Policies ](../../user-guide/access-policies/overview.md)See the Aembit User Guide

→

***

### Trust Providers

[Section titled “Trust Providers”](#trust-providers)

Instead of Client Workloads managing and presenting a long-lived secret for authentication, Aembit uses [Trust Providers](trust-providers.md) to cryptographically verify the identity of Client Workloads attempting to access target Server Workloads. Trust Providers verify a Client Workload’s identity using evidence obtained directly from its runtime environment—also known as workload attestation**Workload Attestation**: Workload attestation cryptographically verifies a workload's identity using evidence from its runtime environment, such as platform identity documents or tokens, rather than using static credentials.[Learn more](trust-providers.md).

Aembit integrates with many Trust Providers to support attestation across different environments:

* AWS
* Azure
* Kubernetes
* CI/CD platforms
* Aembit Agent Controller in Kerberos environments

Trust Providers supply cryptographically signed evidence, such as platform identity documents or tokens, about the Client Workload to Aembit Cloud. Aembit Cloud then validates this evidence to confirm the workload’s identity before proceeding with access policy evaluation. Upon successful attestation, Aembit Cloud gains high confidence in the Client Workload’s identity without relying on a shared secret.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Trust Providers ](trust-providers.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Trust Providers ](../../user-guide/access-policies/trust-providers/overview.md)See the Aembit User Guide

→

***

### Access Conditions

[Section titled “Access Conditions”](#access-conditions)

Aembit uses [Access Conditions](access-conditions.md) to provide a mechanism for adding dynamic, context-aware constraints to Access Policies—similar to Multi-Factor Authentication (MFA) for human identities.

Access Conditions allow Access Policies to incorporate rapid environmental or operational factors into the access decision. For example:

* **Time** - restrictions based on the time of day or day of the week
* **GeoIP** - geographic location of the requesting workload

During \[Access Policy evaluation]\(/get-started/how-aembit-works#access-policy-flow-putting-it-all together), after Aembit Cloud matches the Client and Server Workloads to an Access Policy *and* it verifies the Client Workload’s identity, Aembit Cloud explicitly evaluates all associated Access Conditions. Only if all Access Conditions pass, along with the Client Workload’s identity check, does the Access Policy grant access and trigger the Credential Provider.

Aembit also integrates with external security posture management tools, such as Wiz or CrowdStrike. This allows Access Policies to enforce conditions such as “Aembit only grants access if Wiz reports a healthy security posture for that Client Workload.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Access Conditions ](access-conditions.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Access Conditions ](../../user-guide/access-policies/access-conditions/overview.md)See the Aembit User Guide

→

***

### Credential Providers

[Section titled “Credential Providers”](#credential-providers)

Aembit uses [Credential Providers](credential-providers.md) to facilitate secure authentication between workloads. Credential Providers generate and manage the credentials needed for a Client Workload to authenticate to a Server Workload when an Access Policy determines to grant a Client Workload access. Credential Providers abstract away the complexity of different authentication mechanisms and credential types, providing a consistent interface for workload-to-workload authentication regardless of the underlying systems.

When an Access Policy evaluation succeeds, Aembit Cloud triggers the Credential Provider to generate the appropriate credentials for the specific authentication mechanism that the target Server Workload requires. This interaction is what allows a Client Workload to authenticate to a Server Workload without storing or managing long-lived credentials. This design limits exposure and prevents credential sprawl.

Aembit supports many types of Credential Providers to accommodate different authentication requirements:

* **Basic Authentication** - For systems requiring username/password authentication
* **OAuth 2.0** - For modern API authentication flows
* **API Key** - For services using API key-based authentication
* **Certificate-Based** - For systems requiring mutual TLS authentication
* **Cloud Provider Credentials** - For accessing cloud services (AWS, Azure, GCP) through Workload Identity Federation (WIF)
* **SAML** - For enterprise federated authentication scenarios
* **Kubernetes Tokens** - For Kubernetes-based workloads

You can also set up Credential Providers for external secrets management systems like HashiCorp Vault, AWS Secrets Manager, or Azure Key Vault to retrieve sensitive authentication material when needed.

To provide **credential lifecycle management** capabilities, Aembit offers [Credential Provider integrations](../../user-guide/access-policies/credential-providers/integrations/overview.md) with services like GitLab to create, rotate, and delete access credentials on your behalf.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Credential Providers ](credential-providers.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Set up Credential Providers ](../../user-guide/access-policies/credential-providers/overview.md)See the Aembit User Guide

→

***

## Observability

[Section titled “Observability”](#observability)

Aembit logs every access request (Access Authorization Events) and administrative change. These logs help you understand what’s happening, troubleshoot problems, and meet compliance goals and requirements.

Key event types include:

* **Audit Logs:** Track administrative changes to the platform.
* **Workload Events:** Provide high-level visibility into workload interactions.
* **Access Authorization Events:** Offer **detailed, step-by-step visibility** into policy evaluation for each access request. These logs show Client/Server identification, the outcome of **Trust Provider attestation** (identity verification), **Access Conditions verification** (contextual checks), **Credential Provider retrieval**, and the final **Allow/Deny verdict**. This granularity is essential for **troubleshooting access issues**.

Aembit logs the following:

* Each request’s source, destination, and decision.
* The specific policy that allowed or blocked access.
* Details about which Trust Provider verified an identity.
* What credential Aembit delivered (or why it didn’t).

You can view this information in your Aembit Tenant UI or export it to external log systems for long-term storage and analysis by setting up a [Log Stream](../../user-guide/administration/log-streams/overview.md).

See [Audit and report](audit-report.md)

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Auditing ](audit-report.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Audit Aembit logs ](../../user-guide/audit-report/overview.md)See the Aembit User Guide

→

***

## Aembit’s architecture

[Section titled “Aembit’s architecture”](#aembits-architecture)

Aembit consists of two cooperating systems: [Aembit Edge](#aembit-edge) and [Aembit Cloud](#aembit-cloud).

Aembit Edge communicates with Aembit Cloud to handle authentication and authorization of access between your workloads.

Separating the control plane and the data plane enables you to centralize policy management in the cloud while keeping the enforcement mechanism close to the workloads in your environments. The interception model employed by Aembit Edge is key to enabling the “No-Code Auth” capability.

### Aembit Edge

[Section titled “Aembit Edge”](#aembit-edge)

Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](aembit-edge.md) acts as the **data plane** or interception point and runs alongside Client Workloads in your infrastructure (such as a Kubernetes cluster).

The primary function of Aembit Edge is to intercept outbound network requests from Client Workloads destined for target Server Workloads.

Upon interception, Aembit Edge sends requests from Client Workloads to Aembit Cloud which handles the authentication and authorization of that request. If Aembit Cloud approves access, then Aembit Edge does the following:

1. Receives a credential from Aembit Cloud.
2. Injects the credential into the original request “just-in-time.”
3. Forwards the modified request to the intended target Server Workload.

Aembit Edge also sends detailed access event logs to Aembit Cloud for auditing purposes.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Aembit Edge ](aembit-edge.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Aembit Edge ](../../user-guide/deploy-install/overview.md)See the Aembit User Guide

→

***

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](aembit-cloud.md) acts as the **control plane** and receives requests intercepted by Aembit Edge.

Aembit Cloud determines whether to authorize Client Workload requests and what credential to deliver.

The primary functions of Aembit Cloud are to:

1. Evaluate access requests.
2. Authenticate Client Workloads and attest their identities through a [Trust Provider](trust-providers.md).
3. Enforce [Access Policies](access-policies.md) (including [Access Conditions](access-conditions.md) such as GeoIP or time).
4. Interact with external [Credential Providers](credential-providers.md) to obtain and issue necessary credentials.
5. Communicate access decisions to Aembit Edge.

You can [administer Aembit Cloud](administration.md) through your unique, and isolated Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](administration.md) to define access rules, configure trust and credential sources, and monitor access events.

Aembit Cloud logs all Access Authorization Events so you can [audit and report](audit-report.md) metadata related to access control.

Aembit only logs metadata

Crucially, Aembit functions purely as a control plane; it doesn’t process or log any actual data from your workloads, only metadata related to access control.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Aembit Cloud ](aembit-cloud.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Aembit Cloud ](../../user-guide/access-policies/overview.md)See the Aembit User Guide

→

***

## Administration

[Section titled “Administration”](#administration)

Administration in Aembit provides a comprehensive framework for managing security policies, credentials, and access controls across your organization to control and monitor how your users access and use Aembit. To administer Aembit, you can do so through your unique, dedicated environment—your [Aembit Tenant](#about-aembit-tenants).

Aembit’s Administration UI provides centralized management of all Aembit’s primary components, including Access Policies. Additionally, you can configure and manage advanced Aembit Edge Component features such as TLS Decrypt, PKI-based TLS, proxy steering methods, and more.

Aembit’s administration system follows a Role-Based Access Control (RBAC) model, allowing you to delegate specific administrative responsibilities while maintaining the principle of least privilege.

Aembit’s administration capabilities include:

* **Admin Dashboard** - A central interface providing visibility into system status, recent activities, and security alerts.
* **Users** - Management of human users who interact with the Aembit administrative interface.
* **Roles** - Predefined and custom sets of responsibilities that you can assign to your users to control their administrative access.
* **Permissions** - Granular controls that define what actions your users can perform within your Aembit Tenant.
* **Discovery** - Tools for identifying and cataloging workloads across your infrastructure.
* **Resource Sets** - Logical groupings of resources that help organize and manage access at scale across your environment.
* **Log Streams** - Configuration for sending security and audit logs to external monitoring systems.
* **Identity Providers** - Integration with external identity systems for authenticating administrators.
* **Sign-On Policies** - Rules governing how administrators authenticate to the Aembit system.

### About Aembit Tenants

[Section titled “About Aembit Tenants”](#about-aembit-tenants)

Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.

Each tenant operates independently with its own set of:

* **Administrative Users** - Users who manage the tenant have no access to other tenants.
* **Resources** - All workloads, policies, and configurations are tenant-specific.
* **Security Boundaries** - Complete isolation makes sure configurations in one tenant can’t affect others.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Administration ](administration.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Configure Admin settings ](../../user-guide/administration/overview.md)See the Aembit User Guide

→

***

## Aembit Terraform Provider

[Section titled “Aembit Terraform Provider”](#aembit-terraform-provider)

Aembit supports scalable, repeatable infrastructure-as-code (IaC) workflows through the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest).

Terraform gives you the ability to:

* Codify access policies and workload identity configuration.
* Version control changes to your identity and access infrastructure.
* Apply changes consistently across staging, production, and multicloud environments.
* Automate onboarding for new workloads, trust providers, and credential integrations.

This helps reduce manual steps, eliminate configuration drift, and ensure your access policies are reproducible and reviewable.

The Aembit Terraform Provider supports all core Aembit resources:

| Resource Type        | Terraform Support            |
| -------------------- | ---------------------------- |
| Trust Providers      | ✅ Create and configure       |
| Client Workloads     | ✅ Manage identity matching   |
| Server Workloads     | ✅ Define endpoints, auth     |
| Credential Providers | ✅ Integrate secrets/tokens   |
| Access Policies      | ✅ Authorize workload access  |
| Access Conditions    | ✅ Enforce dynamic controls   |
| Resource Sets        | ✅ Segment environments       |
| Roles & Permissions  | ✅ Assign fine-grained access |

This full coverage enables you to declare your Aembit configuration as code, just like cloud resources or Kubernetes objects.

![](https://docs.aembit.io/aembit-icons/lightbulb-light.svg)

[More on Aembit & Terraform ](scaling-terraform.md)See Core Concepts

→

![](https://docs.aembit.io/aembit-icons/gears-light.svg)

[Scale with Terraform ](../../user-guide/access-policies/advanced-options/terraform/terraform-configuration.md)See the Aembit User Guide

→

***

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Access Policies](access-policies.md)
* [Audit and report](audit-report.md)
* [Administering Aembit](administration.md)
* [Scaling with Terraform](scaling-terraform.md)
