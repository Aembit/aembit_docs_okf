---
type: explanation
title: "About the Oracle Database protocol"
description: "Understand how Aembit connects to Oracle databases, what versions Aembit supports, and how TLS connections work"
resource: https://docs.aembit.io/user-guide/deploy-install/databases/about-oracle-databases/
tags: ["database", "deploy-install"]
timestamp: 2026-05-01T09:18:39-07:00
---

# About the Oracle Database protocol

This page explains how Aembit’s Oracle Database protocol support works, which Oracle versions and client types Aembit supports, and what limitations apply.

Aembit’s Agent Proxy intercepts the Oracle Transparent Network Substrate (TNS) wire protocol and injects database credentials at connection time. Your applications connect to Oracle databases as they normally would. Aembit handles credential provisioning transparently, eliminating static database passwords.

For step-by-step setup instructions, see [Create an Oracle Database Server Workload](../../access-policies/server-workloads/guides/oracle-database.md).

## How Aembit connects to Oracle databases

When your application opens a connection to an Oracle database, the Aembit Agent Proxy on the same Linux VM intercepts the TNS connection through [transparent steering](../advanced-options/agent-proxy/selective-transparent-steering.md). Agent Proxy identifies the connection as an Oracle TNS protocol request, retrieves credentials from a Credential Provider, and injects them into the TNS authentication packets before forwarding the connection to the Oracle database.

The credential injection happens during the Oracle authentication handshake (O5LOGON flow). Aembit supports passwords stored by Oracle in the 12C password verifier format. Older password verifier formats (11G, 10G) aren’t supported. The only change to your client configuration is using `aembit` as the password. Your application doesn’t require driver modifications.

![How Aembit Agent Proxy intercepts and authenticates Oracle TNS connections](https://docs.aembit.io/d2/docs/user-guide/deploy-install/databases/about-oracle-databases-0.svg)

## Supported versions

Aembit supports Oracle Database **19c** and **21c**. Oracle 19c is the most widely deployed version in enterprise environments due to its long-term support status, and Oracle 21c covers organizations using innovation releases. Both versions support the same O5LOGON authentication flow and 12C password version, so Aembit’s credential injection works identically for both.

| Aspect                       | Oracle 19c            | Oracle 21c         |
| ---------------------------- | --------------------- | ------------------ |
| **Release type**             | Long-Term Release     | Innovation Release |
| **Premier Support**          | Through December 2029 | Through July 2027  |
| **Extended Support**         | Through December 2032 | Not available      |
| **Default password version** | 12C                   | 12C                |
| **Authentication protocol**  | O5LOGON               | O5LOGON            |
| **AWS RDS availability**     | Yes                   | Yes                |

Oracle 23ai isn’t supported.

## Supported environments

All environments require Agent Proxy deployed on a Linux VM with [transparent steering](../advanced-options/agent-proxy/selective-transparent-steering.md) configured. Aembit has tested Oracle Database protocol support in the following environments.

| Environment                     | Status       |
| ------------------------------- | ------------ |
| AWS RDS for Oracle              | Supported    |
| Containerized Oracle instances  | Supported    |
| Linux VM (on-premises or cloud) | Supported    |
| Docker-compose on Linux VMs     | Supported    |
| Oracle Database\@Azure          | Not verified |
| Oracle on GCP                   | Not verified |
| Oracle on OCI                   | Not verified |

For the complete list of supported deployment models, see the [support matrix](../../../reference/support-matrix.md).

## Thin vs thick clients

Oracle database drivers come in two variants: **thin** (pure language implementation) and **thick** (using Oracle Client libraries). Aembit supports thin clients. Aembit doesn’t support thick clients (Oracle Client / OCI).

![Thin client connection path through Aembit Agent Proxy](https://docs.aembit.io/d2/docs/user-guide/deploy-install/databases/about-oracle-databases-1.svg)

### Driver packages by language

Aembit supports Java, Python, Go, and Node.js thin drivers.

| Language    | Package                         | Notes                                                    |
| ----------- | ------------------------------- | -------------------------------------------------------- |
| **Java**    | `ojdbc11.jar` (or `ojdbc8.jar`) | Thin mode by default                                     |
| **Python**  | `oracledb`                      | Use default thin mode; don’t call `init_oracle_client()` |
| **Go**      | `godror`                        | Thin mode (`godror.NewConnector` without instant client) |
| **Node.js** | `oracledb`                      | Set `oracledb.initOracleClient` to false or omit it      |

For guidance on thin and thick mode configuration, see [Oracle’s driver documentation](https://docs.oracle.com/en/database/).

## TLS connections

Aembit supports TLS for Oracle database connections using Oracle’s TCPS (TCP/IP with TLS) protocol. TLS protects both sides of the proxy connection:

* **Client to proxy**: Your application connects to Agent Proxy over TCPS.
* **Proxy to database**: Agent Proxy connects to the Oracle database over TCPS.

You enable TLS per Server Workload by checking the **TLS** checkbox on the **Port** and **Forward to Port** fields. For step-by-step configuration instructions, see [Create an Oracle Database Server Workload](../../access-policies/server-workloads/guides/oracle-database.md).

### How Oracle TLS works

TLS is established before the TNS handshake, when the client connects to the Oracle TNS listener. When the TNS listener directs the connection to the database instance, Agent Proxy renegotiates TLS mid-TNS-handshake. Agent Proxy handles both the initial TLS setup and renegotiation transparently, without customer configuration.

### System trust store requirement

For the proxy-to-database TLS connection, Agent Proxy validates the Oracle database’s TLS certificate using the Linux VM’s system trust store.

If your Oracle database uses a certificate from a private or enterprise certificate authority (CA), add that CA certificate to Agent Proxy VM’s system trust store. Add it before enabling TLS on the Server Workload.

On Ubuntu and Debian:

```shell
sudo cp your-oracle-ca.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```

On RHEL and CentOS:

```shell
sudo cp your-oracle-ca.crt /etc/pki/ca-trust/source/anchors/
sudo update-ca-trust
```

> **AWS RDS certificates**
>
> AWS RDS for Oracle uses certificates from Amazon’s certificate authority. Download and install the RDS CA bundle from the [Amazon RDS SSL/TLS documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html) into your system trust store if not already present.

## Authentication

Aembit supports **username/password authentication** only.

Agent Proxy intercepts the Oracle TNS authentication handshake and replaces placeholder credentials with real database credentials from the Credential Provider. This uses the O5LOGON authentication flow with password version 12C.

Your applications must use `aembit` as the password in their connection configuration. Agent Proxy uses this value to derive a shared key for the Oracle authentication handshake. It then replaces the credentials with the real username and password from the Credential Provider. The username can be any value—Agent Proxy replaces it during credential injection.

## Limitations

The following limitations apply to Oracle Database protocol support:

| Limitation                   | Details                                                                                                              |
| ---------------------------- | -------------------------------------------------------------------------------------------------------------------- |
| **Steering mode**            | [Transparent steering](../advanced-options/agent-proxy/selective-transparent-steering.md) only |
| **Deployment model**         | Linux VM (with optional Docker-compose); Kubernetes\*                                                                |
| **Authentication**           | Username/password (12C verifier) only. Kerberos, Oracle Wallet, and OAuth 2.0 aren’t supported.                      |
| **Client type**              | Thin client only. Thick client (Oracle Call Interface / OCI) isn’t supported.                                        |
| **Oracle versions**          | 19c and 21c only. Oracle 23ai isn’t supported.                                                                       |
| **Cloud environments**       | AWS RDS and containerized instances tested. Aembit hasn’t tested Azure, GCP, or OCI environments.                    |
| **Native Oracle encryption** | Not supported. Use TCPS (TLS) instead.                                                                               |

> \* *Kubernetes support requires [transparent steering](../advanced-options/agent-proxy/selective-transparent-steering.md) configured for the Oracle database host.*

For the latest supported capabilities, see the [support matrix](../../../reference/support-matrix.md).

## Related resources

### How-to guide

* [Create an Oracle Database Server Workload](../../access-policies/server-workloads/guides/oracle-database.md): Step-by-step setup instructions, including TLS configuration

### Reference

* [Support matrix](../../../reference/support-matrix.md): Supported deployment models for Oracle Database
* [Transparent steering](../advanced-options/agent-proxy/selective-transparent-steering.md): Steering mode configuration
