---
type: how-to
title: "Source IP Address"
description: "How to identify client workloads using Source IP address within Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/source-ip/
interface: web-ui
tags: ["vm", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Source IP Address

This page explains how to use the **Source IP Address** identifier to uniquely identify client workloads in Aembit.

## Understanding the source IP address identifier

The Source IP Address refers to the IP address from which a client workload initiates a connection. This approach is only suitable in environments where workloads have stable private IP addresses. For example, administrators can assign static IPs or control dynamic assignment using mechanisms like DHCP reservations or IP pools. In such setups, the Source IP Address can serve as a reliable and straightforward identifier for client workloads.

This method is especially useful in environments where other identifiers (such as cloud metadata) are unavailable or hard to access.

Note that Source IP Address-based identification is only as consistent as the network topology and IP management practices.

## Applicable deployment types

Aembit supports Source IP Address-based identification for multiple deployment scenarios, including:

* Edge deployments in private data centers
* Virtual Machines or containers running on IaaS providers (AWS, Azure, GCP)
* Hybrid or on-premise workloads with stable internal IP addressing

## Create a client workload with a source IP address identifier

To configure a Client Workload using the Source IP Address identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **Source IP Address**.

   For **Value**, enter the **private IP address** that the Client Workload uses to initiate outbound connections.

   Example: `10.0.42.17`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Client Workload identifier uniqueness**
>
> When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.
>
> To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.
>
> See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Finding the source IP address

To identify the Source IP Address of a workload, use the IP address assigned to its primary network interface. On virtual machines, this is typically the IP associated with `eth0`, `ensX`, or a similar interface.

This IP should match the one used by the workload when initiating outbound connections through the Aembit Edge Proxy.

## Related

**Compatible trust providers**

* [AWS Metadata Service](../../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
* [Kerberos](../../trust-providers/kerberos-trust-provider.md)
* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
