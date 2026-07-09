---
type: explanation
title: "Database protocol support"
description: "Deployment requirements and configuration for database protocols supported by Aembit Agent Proxy"
resource: https://docs.aembit.io/user-guide/deploy-install/databases/
tags: [database, deploy-install]
timestamp: 2026-02-26T15:41:06-08:00
---

# Database protocol support

Aembit Agent Proxy supports multiple database wire protocols, enabling credential injection without application code changes. Each protocol has specific deployment requirements and configuration steps.

This section covers deployment-level context for database protocol support: environment requirements, certificate configuration, and protocol-specific details.

For step-by-step instructions on creating database Server Workloads in the Aembit Tenant, see the [Server Workloads guides](../../access-policies/server-workloads/guides/overview.md).

## Supported database protocols

[Section titled “Supported database protocols”](#supported-database-protocols)

| Protocol                       | Deployment models                    | Details                                                                               |
| ------------------------------ | ------------------------------------ | ------------------------------------------------------------------------------------- |
| Oracle Database (Limited Beta) | Kubernetes, Linux VM, Docker-compose | [About Oracle Databases](about-oracle-databases.md) |
| MySQL                          | Kubernetes, Linux VM, Docker-compose | See [support matrix](../../../reference/support-matrix.md)                                       |
| PostgreSQL                     | Kubernetes, Linux VM, Docker-compose | See [support matrix](../../../reference/support-matrix.md)                                       |
| Redis                          | Kubernetes, Linux VM, Docker-compose | See [support matrix](../../../reference/support-matrix.md)                                       |
| Amazon Redshift                | Kubernetes, Linux VM, Docker-compose | See [support matrix](../../../reference/support-matrix.md)                                       |

## In this section

[Section titled “In this section”](#in-this-section)

* [About Oracle Databases](about-oracle-databases.md) — How Aembit connects to Oracle databases, supported versions, and client types
* [Install AWS RDS certificates](aws-rds.md) — Install the AWS RDS CA certificates required for MySQL, PostgreSQL, and Redshift connections in AWS
