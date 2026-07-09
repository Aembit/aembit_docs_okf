---
type: how-to
title: "How to configure a Standalone CA"
description: "How to configure Standalone CA for TLS Decrypt"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca/
interface: web-ui
tags: [tls-decrypt, advanced-option, deploy-install]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# How to configure a Standalone CA

To configure a [Standalone CA](configure-tls-decrypt-standalone-ca.md), you must first [create a Standalone CA](#how-to-create-a-standalone-ca) then assign it to your desired resources:

* [Resource Set](#assign-a-standalone-ca-to-a-resource-set)

* [Client Workload](#assign-a-standalone-ca-to-a-client-workload)

Paid feature

Standalone CAs are a paid feature. Please contact your Aembit representative for more information about pricing and implementation.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

* [Aembit Role](../../../administration/roles/overview.md) with the following **Read/Write** permissions:

  * `Standalone Certificate Authorities`

  * `Client Workloads`

  * `Resource Sets`

Optional

If you’ve never configured Standalone CA for TLS Decrypt before, Aembit recommends that you read [Standalone CA behavior](about-tls-decrypt-standalone-ca.md#standalone-ca-behavior) to familiarize yourself with how Standalone CAs interact with Resource Sets.

## How to create a Standalone CA

[Section titled “How to create a Standalone CA”](#how-to-create-a-standalone-ca)

Follow these steps to create a Standalone CA:

1. Log into your Aembit Tenant, and go to **Edge Components -> TLS Decrypt**.

2. In the top right corner, select the **Resource Set** where you want your Standalone CA to reside.

   ![TLS Decrypt screen with Standalone Certificate Authorities list](https://docs.aembit.io/_astro/tls_decrypt-standalone-ca.DfZ1qNHE_274d2S.webp)

3. In the **Standalone Certificate Authorities** section, click **+ New**.

   This displays the **Standalone Certificate Authority** pop out menu:

   ![New Standalone Certificate Authority pop out menu](https://docs.aembit.io/_astro/tls_decrypt-standalone-ca-new.CqlmcMy2_ABlDX.webp)

4. Enter a **Name** and optional **Description**.

5. Select the lifetime you desire from the **Leaf Certificate Lifetime options** dropdown.

6. Click **Save**.

   Aembit displays your new Standalone CA in the **Standalone Certificate Authorities** table.

## Assign a Standalone CA to a Resource Set

[Section titled “Assign a Standalone CA to a Resource Set”](#assign-a-standalone-ca-to-a-resource-set)

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Resource Sets**.

4. Click the **Resource Set** that you want to assign a Standalone CA, then click **Edit**.

   Or follow [Create a new Resource Set](../../../administration/resource-sets/create-resource-set.md) to create one.

   ![Edit Resource Set screen with Standalone Certificate Authority section](https://docs.aembit.io/_astro/resource-set-standalone-ca.CN4dd0lm_Z2lCtg3.webp)

   Note

   If you don’t see the Standalone CA that you want to assign, the Standalone CA may reside in a different Resource Set.

5. In the **Standalone Certificate Authority** section, select the Standalone CA you want to assign to the Resource Set.

6. Click **Save**.

## Assign a Standalone CA to a Client Workload

[Section titled “Assign a Standalone CA to a Client Workload”](#assign-a-standalone-ca-to-a-client-workload)

1. Log into your Aembit Tenant, and go to **Client Workloads**.

2. In the top right corner, select the **Resource Set** where the Standalone CA you want to assign resides.

Caution

It’s crucial that you select the correct Resource Set, or you may not see your Standalone CA when assigning it. Or worse, you may assign the wrong Standalone CA to your Client Workload.

1. Select the Client Workload you wan to assign the Standalone CA to, then click **Edit**.

   ![Edit Client Workload screen with Standalone Certificate Authority](https://docs.aembit.io/_astro/cw-standalone-ca.BMVK2u3w_1ibAVk.webp)

2. In the **Standalone Certificate Authority** section, select the Standalone CA you want to assign to the Client Workload.

3. Click **Save**.

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [About Standalone CA for TLS Decrypt](configure-tls-decrypt-standalone-ca.md)

* [About TLS Decrypt](overview.md)
