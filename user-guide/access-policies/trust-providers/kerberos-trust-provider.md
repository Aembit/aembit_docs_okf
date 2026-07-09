---
type: how-to
title: "Kerberos Trust Provider"
description: "How to configure a Kerberos Trust Provider"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/kerberos-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2026-02-23T07:23:16-08:00
type_inferred: true
---

# Kerberos Trust Provider

The Kerberos Trust Provider enables the attestation of Client Workloads running on virtual machines (VMs) joined to Active Directory (AD).

This attestation method is specifically designed for on-premise deployments where alternative attestation methods, such as AWS or Azure metadata service Trust Providers, aren’t available.

This Trust Provider is unique because it relies on attestation provided by an Aembit component, rather than attestation from a third-party system. In this scenario, the Aembit Agent Controller acts as the attesting system. It authenticates a client (specifically, Agent Proxy) via Kerberos and attests to the client’s identity. The client’s identity information is then signed by the Aembit Agent Controller and validated by Aembit Cloud as part of the access policy evaluation process.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Many prerequisites are necessary, particularly regarding domain users and principals. This page outlines Aembit’s current recommendations for a secure and scalable deployment.

Kerberos based attestation is available only for [Virtual Machine Deployments](../../deploy-install/virtual-machine/overview.md).

### Join your Edge Components to AD domain

[Section titled “Join your Edge Components to AD domain”](#join-your-edge-components-to-ad-domain)

* You must join Agent Controller VMs to AD before you install Agent Controller on them.

* You must join Client Workload VMs to AD before installing Agent Proxy.

### Domain users and service principals

[Section titled “Domain users and service principals”](#domain-users-and-service-principals)

* You must create a user in AD named `aembit_ac` for Agent Controllers. This user doesn’t need any specific permissions in AD.

* You must create a service principal for the Agent Controller under the `aembit_ac` AD user.

  * For testing purposes, create a service principal `HTTP/<Agent-Controller-hostname>`.

  * For production purposes, see [High Availability](#high-availability).

* Agent Controllers on Windows Server in high availability (HA) configurations, must set the `SERVICE_LOGON_ACCOUNT` environment variable to an AD user in [Down-Level Logon Name format](https://learn.microsoft.com/en-us/windows/win32/secauthn/user-name-formats#down-level-logon-name) (for example: `SERVICE_LOGON_ACCOUNT=<DOMAIN>\<sAMAccountName>$`).

### Network access

[Section titled “Network access”](#network-access)

* Agent Controller VMs don’t need access to the Domain Controller.

* Client Workload VMs must have access to the Domain Controller to acquire tickets.

### Keytabs

[Section titled “Keytabs”](#keytabs)

* Agent Controller

  * Agent Controller Linux VMs require a keytab file for the Agent Controller AD user.

  * You can place the keytab file on the VM before or after the Agent Controller installation.

  * The Agent Controller Linux user must have read/write permissions on the keytab file (`aembit_agent_controller`). If you place a keytab file before you install the Agent Controller, Aembit recommends creating a Linux group `aembit` and a Linux user `aembit_agent_controller`, and making this file accessible by this Linux user/group.

  * If your organization has mandatory AD password rotation, make sure you have a configuration in place for keytab renewal.

    See [Agent Controller keytab rotation](#agent-controller-keytab-rotation-for-high-availability-deployment) for more information.

* Agent Proxy

  * The Agent Proxy on the Client Workload machine uses the host keytab file.

  * The Agent Proxy uses the [sAMAccountName](https://learn.microsoft.com/en-us/windows/win32/ad/naming-propertes#samaccountname) principal from the host keytab.

  * The host keytab can have Linux root:root ownership.

## Kerberos Trust Provider match rules

[Section titled “Kerberos Trust Provider match rules”](#kerberos-trust-provider-match-rules)

The Kerberos Trust Provider supports the following match rules:

* Principal

* Realm/Domain

* Source IP

  Important

  When matching on Principal or Realm/Domain, see [Kerberos Principal formatting](#kerberos-principal-formatting) for guidance.

| Data      | Description                                            | Example                        |
| --------- | ------------------------------------------------------ | ------------------------------ |
| Principal | The Agent Proxy’s VM principal                         | `IP-172-31-35-14$@EXAMPLE.COM` |
| Realm     | The realm of the Client Workload VM principal          | `EXAMPLE.COM`                  |
| Domain    | The NetBIOS domain of the Client Workload VM principal | `example`                      |
| Source IP | The Network Source IP address of the Client request    | `192.168.1.100`                |

### Associated Agent Controllers

[Section titled “Associated Agent Controllers”](#associated-agent-controllers)

During the configuration of the Kerberos Trust Provider, you must specify the list of Agent Controllers responsible for providing attestation. Aembit trusts only the attestation information signed by specified Agent Controllers by a Kerberos Trust Provider entry.

### Kerberos Principal formatting

[Section titled “Kerberos Principal formatting”](#kerberos-principal-formatting)

Aembit supports Agent Controller on Windows VMs to improve management of the Aembit Edge Components. This is especially true for [Agent Controller high availability configurations](../../deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md) that use Windows [Group Managed Service Accounts (gMSA)](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/group-managed-service-accounts/group-managed-service-accounts/group-managed-service-accounts-overview) to manage multiple Agent Controllers.

The challenge is that Windows and Linux systems treat AD differently, in that Linux treats it purely as Kerberos and Windows treats it natively like AD. This results in different naming and formatting for the Kerberos Principal value that Aembit uses in Kerberos tokens which it exchanges for AD authentication.

The following table details all the combinations you can encounter based on the OS installed on Agent Controller and Agent Proxy:

| OS combination                                         | Principal format                          |
| ------------------------------------------------------ | ----------------------------------------- |
| **Linux** Agent Controller + **Linux** Agent Proxy     | `<sAMAccount name>@<realm>`               |
| **Linux** Agent Controller + **Windows** Agent Proxy   | `<User name>@<realm>`                     |
| **Windows** Agent Controller + **Linux** Agent Proxy   | `<NetBIOS domain name>\<sAMAccount name>` |
| **Windows** Agent Controller + **Windows** Agent Proxy | `<NetBIOS domain name>\<User name>`       |

As part of the Kerberos Trust Provider attestation process and to address this challenge, Aembit Cloud automatically parses the attested Kerberos Principal value and *verifies either the realm or the domain* from the value for you.

## Enable Kerberos attestation

[Section titled “Enable Kerberos attestation”](#enable-kerberos-attestation)

By default, Aembit disables Kerberos attestation on both Agent Controller and Agent Proxy.

Follow the applicable sections to enable Kerberos attestation on Aembit Edge Components:

### Agent Controller on Windows Server

[Section titled “Agent Controller on Windows Server”](#agent-controller-on-windows-server)

To enable Kerberos attestation for [Agent Controller on a Windows Server VM](../../deploy-install/virtual-machine/windows/agent-controller-install-windows.md), you must set the following environment variables:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
SERVICE_LOGON_ACCOUNT=<DOMAIN>\<sAMAccountName>$
```

### Agent Controller on Linux

[Section titled “Agent Controller on Linux”](#agent-controller-on-linux)

To enable Kerberos attestation for [Agent Controller on a Linux VM](../../deploy-install/virtual-machine/linux/agent-controller-install-linux.md), you must set the following environment variables:

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
KRB5_KTNAME=<Agent Controller keytab file location>
```

### Agent Proxy

[Section titled “Agent Proxy”](#agent-proxy)

Similarly, the Agent Proxy installer requires the following environment variable (in addition to the standard variables provided during [installation](../../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md)):

```shell
AEMBIT_KERBEROS_ATTESTATION_ENABLED=true
AEMBIT_PRIVILEGED_KEYTAB=true
```

## TLS

[Section titled “TLS”](#tls)

The contents of the communication between Agent Proxy and Agent Controller is sensitive. In a production deployment, you may configure Agent Controller TLS to secure communication between these two components using either a Customer’s PKI or Aembit’s PKI. Please see the following pages for more information on using a PKI in your configuration:

* [Configure a Customer’s PKI Agent Controller TLS](../../deploy-install/advanced-options/agent-controller/configure-customer-pki-agent-controller-tls.md)

* [Configure Aembit’s PKI Agent Controller TLS](../../deploy-install/advanced-options/agent-controller/configure-aembit-pki-agent-controller-tls.md)

## High availability

[Section titled “High availability”](#high-availability)

Given the critical role of attestation in evaluating an Access Policy, Aembit strongly encourages configuring multiple Agent Controllers in a high availability architecture.

To learn how, see [Agent Controller High Availability](../../deploy-install/advanced-options/agent-controller/agent-controller-high-availability.md).

The following are the additional steps you must perform for Kerberos attestation to work in a highly available configuration:

* You don’t need to join the load balancer to your domain.

* You must create a service principal `HTTP/<Load Balancer hostname>` under the Aembit Agent Controller Active Directory user.

* You don’t need to create principals for individual Agent Controller VMs.

* You must place the keytab for the Agent Controller AD user (including the load-balancer service principal) on all Agent Controller VMs.

* If you operate multiple Agent Controller clusters running behind one or more load balancers, you must add each load balancer FQDN as the service principal under Agent Controller AD account.

## Agent Controller keytab rotation for high availability deployment

[Section titled “Agent Controller keytab rotation for high availability deployment”](#agent-controller-keytab-rotation-for-high-availability-deployment)

Standard best practice recommends the periodic rotation of all keytabs.

Considering that Aembit shares the keytab representing an Agent Controller’s identity across multiple Agent Controller machines, the common method of keytab rotation on Linux (using SSSD) isn’t feasible.

Your organization must have a centrally orchestrated keytab rotation, where the Agent Controller AD user keytab is rotated centrally and then pushed to all Agent Controller Virtual Machines. Note that the entity performing the keytab rotation needs the appropriate permissions in AD to change the Agent Controller password during new-keytab creation.

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Aembit Client ID](../client-workloads/identification/aembit-client-id.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
