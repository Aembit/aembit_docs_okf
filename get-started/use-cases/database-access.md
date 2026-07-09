---
type: explanation
title: "Securing database access"
description: "How Aembit replaces static database credentials with identity-based access for PostgreSQL, MySQL, Snowflake, and more"
resource: https://docs.aembit.io/get-started/use-cases/database-access/
tags: [use-case]
timestamp: 2026-04-13T13:05:24-07:00
---

# Securing database access

Databases and data warehouses hold your organization’s most sensitive data, including transaction records, customer information, financial data, and business intelligence. Static database credentials embedded in application code, configuration files, or environment variables create constant risk. If an attacker compromises the application, they gain persistent access to the underlying data stores.

## What Aembit solves

[Section titled “What Aembit solves”](#what-aembit-solves)

Aembit eliminates static database credentials by generating short-lived, database-native credentials on demand using workload identity. Your applications authenticate using their cloud identity (AWS IAM, Azure Managed Identity, Kubernetes service accounts), and Aembit translates that identity into the credentials your database requires.

* Applications never store database passwords, reducing the attack surface from persistent credentials
* Aembit generates credentials your database already understands (PostgreSQL passwords, MySQL tokens, Snowflake OAuth tokens), so there is no need to retrofit federation onto legacy systems
* Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md) verify workload identity before issuing credentials, enforcing least-privilege access to specific databases
* Credentials expire after minutes or hours based on policy, and Aembit issues new ones automatically without manual rotation scripts
* Aembit logs every database access request with workload identity, timestamp, and policy decision, regardless of database type
* You manage access policies for PostgreSQL, MySQL, SQL Server, Snowflake, and MongoDB through a single control plane

How Aembit translates cloud identity into database credentials

Your workload authenticates to Aembit using AWS IAM, Azure Managed Identity, or Kubernetes service accounts. Aembit verifies that identity against your Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../concepts/trust-providers.md) configuration.

Once verified, Aembit generates database-specific credentials (a PostgreSQL username and password, for example). These credentials are valid for a limited time (typically minutes to hours, based on your policy).

The database itself doesn’t need to understand AWS IAM. It receives a standard PostgreSQL authentication request with a username and password. From the database’s perspective, this looks like normal authentication. The difference is that the credentials are dynamically generated, short-lived, and tied to a verified workload identity.

When the credentials expire, the application automatically requests new ones from Aembit. Aembit cleans up the database user and issues a new set of credentials. This rotation happens transparently to your application code.

## Real example: Application accessing production database

[Section titled “Real example: Application accessing production database”](#real-example-application-accessing-production-database)

A customer-facing order management application running in AWS needs to query a PostgreSQL database that stores transaction history and customer records. Without Aembit, the application would use a static database password stored in AWS Secrets Manager or environment variables. Those credentials never rotate and provide a constant attack surface.

![Order management service authenticating through Aembit with AWS IAM identity to receive short-lived PostgreSQL database credentials](https://docs.aembit.io/d2/docs/get-started/use-cases/database-access-0.svg)

With Aembit, the flow works like this:

1. The application workload authenticates to Aembit using its AWS IAM identity (no passwords or keys to manage)
2. Aembit’s Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../concepts/trust-providers.md) verifies the workload identity against policy (which AWS account, which role, which workload attributes)
3. The Access Policy checks whether this workload should access this specific database
4. Aembit’s Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../concepts/credential-providers.md) issues a short-lived PostgreSQL username and password
5. The application uses these credentials to connect to the database
6. When the credentials expire, the application requests new ones automatically
7. Aembit logs every access request, creating an audit trail of which workloads accessed the database

### Why this matters for database security

[Section titled “Why this matters for database security”](#why-this-matters-for-database-security)

The application never stores database passwords. Every credential is short-lived and bound to a specific workload identity. If an attacker compromises the application, they can’t extract long-lived database credentials because none exist.

Database vendors built many enterprise databases before SAML and OAuth existed. These databases authenticate with usernames and passwords or certificate-based authentication, not identity federation. Aembit bridges this gap. Your workloads authenticate with modern cloud identity (AWS IAM, Azure Managed Identity, Kubernetes service accounts), and Aembit translates that into the authentication protocol your database understands.

Why doesn’t every database support federation?

Each database uses different authentication protocols. PostgreSQL uses Salted Challenge Response Authentication Mechanism (SCRAM) or MD5, and MySQL uses its own authentication plugin system. Oracle supports multiple modes (password, Kerberos, certificate-based), and Snowflake uses OAuth tokens. Database vendors designed many of these protocols before modern identity federation standards like SAML and OAuth existed.

Retrofitting federation support into legacy database engines is technically complex and often not feasible for systems that have been in production for decades. Aembit solves this by acting as a translation layer. Workloads authenticate using modern cloud identity, and Aembit generates the legacy credentials the database requires.

### A good starting place for zero trust

[Section titled “A good starting place for zero trust”](#a-good-starting-place-for-zero-trust)

Databases hold your most sensitive data. Securing database access is often where organizations start their zero-trust**Zero trust**: A security model that requires verification of every access request regardless of network location. No user, workload, or device is implicitly trusted, even inside the network perimeter.[Learn more(opens in new tab)](https://csrc.nist.gov/publications/detail/sp/800-207/final) journey. Aembit supports the major enterprise databases and gives you coverage across most of your critical systems. The Trust Providers and Access Policies you configure for database access use the same model for API access, cloud services, and CI/CD pipelines. The identity-based access patterns you build here apply across all workload types, so each subsequent use case is faster to deploy.

## Supported databases

[Section titled “Supported databases”](#supported-databases)

Aembit supports the following databases and data warehouses. Each integration uses database-native authentication protocols, so the database side requires no changes.

**Relational databases:**

* [PostgreSQL](../../user-guide/access-policies/server-workloads/guides/aws-postgres.md)
* [MySQL](../../user-guide/access-policies/server-workloads/guides/aws-mysql.md)
* [Oracle Database](../../user-guide/access-policies/server-workloads/guides/oracle-database.md)

**Data warehouses:**

* [Snowflake](../../user-guide/access-policies/server-workloads/guides/snowflake.md)

For a complete list of supported databases, see [Server Workloads](../../user-guide/access-policies/server-workloads/overview.md).

Why doesn’t Aembit support every database?

Each database uses different authentication protocols. PostgreSQL uses SCRAM or MD5, and MySQL uses its own authentication plugin system. Oracle supports multiple modes (password, Kerberos, certificate-based), and Snowflake uses OAuth tokens. Aembit must implement specific support for each protocol.

Rather than supporting every niche database, Aembit focuses on platforms that represent the majority of enterprise usage, targeting 80%+ coverage. Once you have Oracle, PostgreSQL, MySQL, and major cloud data warehouses, you cover most production databases. Specialized databases (like International Business Machines (IBM) DB2 or legacy systems) go through Aembit’s feature prioritization process based on customer demand.

## Next steps

[Section titled “Next steps”](#next-steps)

* [Quickstart guide](../quickstart/overview.md)
* [Configure your first Access Policy](../../user-guide/access-policies/overview.md)
* [Server Workloads integration guides](../../user-guide/access-policies/server-workloads/overview.md)

**Related use cases:**

* For applications accessing databases across AWS, Azure, and GCP, see [Multicloud access](multicloud.md)
* For securing pipeline access to databases during deployments, see [CI/CD pipelines](ci-cd.md)
* For protecting connections to third-party SaaS APIs (not just databases), see [Third-party SaaS access](third-party-access.md)
