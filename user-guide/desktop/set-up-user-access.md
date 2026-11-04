---
type: how-to
title: "Set up user access for Aembit Desktop"
description: "Enable user access on your Identity Provider and build the Access Policy that Aembit Desktop uses."
resource: https://docs.aembit.io/user-guide/desktop/set-up-user-access/
interface: web-ui
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Set up user access for Aembit Desktop

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Setting up user access is the Aembit Tenant administrator’s half of Aembit Desktop, and you do it once for each Identity Provider that people sign in through. It ends with a deployed Access Policy and an **Aembit Client ID** that you hand to everyone running Aembit Desktop. That value identifies the Identity Provider for Aembit Desktop sign-in. A Client Workload’s Aembit Client ID is a different value. [Install Aembit Desktop and sign in](install.md) covers the other half, which happens on each person’s own machine.

## Before you begin

Before you start, make sure you have:

* Administrator access to your Aembit Tenant.

* An Identity Provider configured for Single Sign-On in your Aembit Tenant. Aembit Desktop signs people in through that Identity Provider, which can be an [OIDC 1.0](../administration/identity-providers/create-idp-oidc.md) or [SAML 2.0](../administration/identity-providers/create-idp-saml.md) Identity Provider.

* An HTTP or HTTPS service that you want people to reach, and the credential that service accepts.

* Access to the default Resource Set. Aembit Desktop supports only the default Resource Set, so create every component on this page there.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## Enable user access on your Identity Provider

Follow [Configure Identity Provider user access](../administration/identity-providers/configure-user-access.md) to enable user access, configure the session lifetimes, and copy the **Aembit Client ID**. Send the Aembit Client ID to the people installing Aembit Desktop, who pass it to `adt login`, or deliver it in a settings file, as described in [Configure Aembit Desktop settings](configure.md).

## Create the Client Workload

The Client Workload is how an Access Policy recognizes that a request came from Aembit Desktop rather than from anything else in your environment.

1. Click **Client Workloads** in the left sidebar, then click **+ New**.

2. Enter a **Name**, such as `Aembit Desktop`.

3. Under **Client Identification**, select **User Access IDP Client ID** from the **Type** dropdown.

   This identifier type appears only after Aembit support enables Aembit Desktop for your Aembit Tenant.

4. Select the Identity Provider you enabled user access on from the **Select Identity Provider** dropdown, then click the plus button to add the identifier.

5. Click **Save**.

## Create the Trust Provider

The Trust Provider decides which signed-in people the Access Policy covers. Aembit Cloud verifies that the person authenticated to your Identity Provider before the Trust Provider runs. The match rules here narrow that set rather than establish it.

1. Click **Trust Providers** in the left sidebar, then click **+ New**.

2. Enter a **Name**, such as `Aembit Desktop users`.

3. Select **User Identity Provider** from the **Trust Provider** dropdown.

4. Under **Match Rules**, select `sub` as the **Attribute** and enter the person’s email address as the **Value**.

   Aembit sets `sub` to the first email address it finds in the NameID, email, or User Principal Name (UPN) claim the Identity Provider sends, in lowercase. When none of them holds an email address, `sub` holds another identifier from the Identity Provider, and a rule that matches an email address doesn’t match. Add a match rule for each person the Access Policy covers.

5. Click **Save**.

## Attach the Server Workload and Credential Provider

The Server Workload is the service people reach, and the Credential Provider supplies the credential Aembit injects into each request. Neither is specific to Aembit Desktop, so create them as you would for any Access Policy:

* [Create a Server Workload](../access-policies/server-workloads/overview.md) for the HTTP or HTTPS service. Aembit Desktop reaches HTTP and HTTPS services only.

* [Create a Credential Provider](../access-policies/credential-providers/overview.md) that issues the credential the service accepts.

## Deploy the Access Policy

1. Click **Access Policies** in the left sidebar, then click **New**.

   The Access Policy Builder opens with the **Access Policy** panel on top.

2. Enter a name for the Access Policy and click **Save**.

   The panel closes and the builder shows a card for each component.

3. Configure the **Client Workload** card with the Client Workload you created, and the **Server Workload** card with the Server Workload.

4. Configure the **Trust Provider** card with the Trust Provider you created.

5. Configure the **Credential Provider** card with the Credential Provider.

6. Click **Save Policy & Activate**.

The Access Policy applies to new requests once it’s active. A request can still follow an earlier answer from Aembit Cloud until that answer expires, as [Injecting a credential](concepts.md#injecting-a-credential) explains. The Aembit Tenant UI needs nothing further.

## Next steps

* [Install Aembit Desktop and sign in](install.md) - What the people you sent the **Aembit Client ID** to do on their own machines
* [Configure Aembit Desktop settings](configure.md) - Deliver the Aembit Client ID and other settings to people’s machines
* [Aembit Desktop reference](reference.md) - The `adt` commands, options, defaults, and limits
* [What Aembit Desktop supports](reference.md#supported-platforms-and-protocols) - The platforms, protocols, and sign-in methods Aembit Desktop supports
