---
type: explanation
title: "About Server Workloads"
description: "Understanding Server Workloads and their role as access targets in Aembit"
resource: https://docs.aembit.io/get-started/concepts/server-workloads/
tags: [concept]
timestamp: 2026-04-13T13:05:24-07:00
type_inferred: true
---

# About Server Workloads

Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](server-workloads.md) represent the target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](client-workloads.md). They’re the “servers” in Aembit’s client-server access model, acting as the resource providers that Client Workloads need to consume services, data, or functionality from.

Server Workloads can be virtually any service that provides functionality to other systems—from modern cloud-native APIs and microservices to legacy on-premises databases, from third-party SaaS platforms like Snowflake and Stripe to AI services like OpenAI and Claude. The key characteristic is that they receive incoming requests and provide responses, making them the targets of access control policies.

The core challenge Server Workloads address is **centralized access management**—providing a unified way to define, configure, and manage access to diverse services regardless of their location, protocol, or authentication requirements. Instead of managing separate authentication configurations for each service, Aembit creates a logical abstraction that standardizes how [Client Workloads](client-workloads.md) access any target service.

![](https://docs.aembit.io/aembit-icons/server-workload.svg)

[Start configuring Server Workloads ](../../user-guide/access-policies/server-workloads/overview.md)See Server Workloads in the User Guide

→

## How Server Workloads work

[Section titled “How Server Workloads work”](#how-server-workloads-work)

The following steps outline how Server Workloads function within Aembit’s access control flow:

1. **Access Request** - A [Client Workload](client-workloads.md) attempts to access a target service (the Server Workload), such as making an API call to a database or third-party service.

2. **Server Workload Identification and Policy Lookup** - Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](aembit-edge.md) intercepts the outbound request and matches the destination (host and port) against configured Server Workload definitions. Aembit Cloud**Aembit Cloud**: Aembit Cloud serves as both the central control plane and management plane, making authorization decisions, evaluating policies, coordinating credential issuance, and providing administrative interfaces for configuration.[Learn more](aembit-cloud.md) then locates the appropriate Access Policy that links the identified Client Workload to the target Server Workload, along with any required Trust Providers**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](trust-providers.md) and Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](access-conditions.md).

3. **Authentication Requirements** - The Server Workload definition specifies what type of authentication the target service expects (such as Bearer tokens, API keys, or database credentials).

4. **Credential Provisioning** - Aembit obtains the required credentials from the configured Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](credential-providers.md), which knows how to generate or retrieve the specific authentication credentials that the target service expects.

5. **Request Forwarding** - Aembit Edge injects the obtained credentials into the Client Workload’s original request (such as adding HTTP headers or modifying connection parameters) and forwards the authenticated request to the actual target service.

6. **Response Handling** - The target service processes the authenticated request and returns its response, which Aembit Edge forwards back to the Client Workload transparently.

The following diagram illustrates this process:

![Server Workload access flow showing how Aembit Edge intercepts requests, looks up policies, obtains credentials, and forwards authenticated requests to the target service](https://docs.aembit.io/d2/docs/get-started/concepts/server-workloads-0.svg)

## Supported environments

[Section titled “Supported environments”](#supported-environments)

Aembit supports virtually any service as a Server Workload, regardless of location, protocol, or authentication method. Aembit’s flexibility allows organizations to centralize access control across their entire technology stack.

The [Server Workload guides](../../user-guide/access-policies/server-workloads/guides/overview.md) provide configuration examples for many common services, but this list isn’t exhaustive. You can configure Aembit to work with any service that accepts network requests.

**Cloud Platforms and APIs**

* [AWS services](../../user-guide/access-policies/server-workloads/guides/aws-cloud.md) - S3, Lambda, and other AWS APIs
* [Microsoft Graph](../../user-guide/access-policies/server-workloads/guides/microsoft-graph.md) - Office 365 and Azure services
* [Google Cloud services](../../user-guide/access-policies/server-workloads/guides/gcp-bigquery.md) - BigQuery and other GCP APIs

**Databases and Data Platforms**

* [Local databases](../../user-guide/access-policies/server-workloads/guides/local-mysql.md) - MySQL, PostgreSQL, Redis on-premises
* [AWS databases](../../user-guide/access-policies/server-workloads/guides/aws-redshift.md) - RDS, Redshift, and other managed databases
* [Snowflake](../../user-guide/access-policies/server-workloads/guides/snowflake.md) - Cloud data warehouse platform
* [Databricks](../../user-guide/access-policies/server-workloads/guides/databricks.md) - Analytics and machine learning platform

**Third-Party SaaS and APIs**

* [Financial services](../../user-guide/access-policies/server-workloads/guides/stripe.md) - Stripe, PayPal payment processing
* [AI and ML platforms](../../user-guide/access-policies/server-workloads/guides/openai.md) - OpenAI, Claude, Gemini APIs
* [Developer tools](../../user-guide/access-policies/server-workloads/guides/github-rest.md) - GitHub, GitLab, Slack APIs
* [Security platforms](../../user-guide/access-policies/server-workloads/guides/okta.md) - Okta, Beyond Identity, GitGuardian

**CI/CD and DevOps**

* [Version control](../../user-guide/access-policies/server-workloads/guides/gitlab-rest.md) - Git repositories and CI/CD platforms
* [Infrastructure tools](../../user-guide/access-policies/server-workloads/guides/hashicorp-vault.md) - HashiCorp Vault, Key Management Service (KMS) services
* [Monitoring platforms](../../user-guide/access-policies/server-workloads/guides/pagerduty.md) - PagerDuty, SauceLabs

**Legacy and On-Premises Systems**

* Any HTTP/HTTPS-based service or API
* Database servers using standard protocols (SQL, NoSQL)
* Custom applications and microservices
* Legacy systems accessible over TCP

## Benefits of using Server Workloads

[Section titled “Benefits of using Server Workloads”](#benefits-of-using-server-workloads)

* **Centralized Access Management** - Provides a single point of control for managing access to diverse services across hybrid and multi-cloud environments.
* **Abstraction from Implementation Details** - Decouples access policies from specific service locations, authentication methods, or infrastructure changes.
* **Standardized Authentication** - Enables consistent authentication patterns regardless of the target service’s native authentication requirements.
* **Simplified Credential Management** - Eliminates the need for Client Workloads to store or manage service-specific credentials.
* **Policy Resilience** - Access policies remain stable even when services change locations, ports, or authentication methods.
* **Audit and Compliance** - Provides comprehensive logging of which workloads accessed which services and when, supporting security monitoring and compliance requirements.
