---
type: how-to
title: "Configure Identity Provider user access"
description: "Enable user access through an Identity Provider and configure Aembit Desktop session lifetimes."
resource: https://docs.aembit.io/user-guide/administration/identity-providers/configure-user-access/
interface: web-ui
tags: ["identity-provider", "administration"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Configure Identity Provider user access

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

User access lets people authenticate to Aembit Desktop through an existing Single Sign-On (SSO) Identity Provider. Enable user access for each Identity Provider that people use to sign in.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## Before you begin

Before you configure user access, make sure you have:

* Administrator access to your Aembit Tenant.
* Aembit Desktop enabled for your Aembit Tenant by Aembit support, which is what makes the **Users** tab appear.
* An existing [OIDC 1.0](create-idp-oidc.md) or [SAML 2.0](create-idp-saml.md) Identity Provider.

## Configure user access

1. Log in to your Aembit Tenant.

2. Click **Administration** in the left sidebar, then select **Identity Providers**.

3. Click the Identity Provider that people use to sign in.

4. Click **Edit**.

5. Select the **Users** tab.

   The tab appears only after Aembit support enables Aembit Desktop for your Aembit Tenant.

6. Turn on **Enable User Access**.

   Aembit displays the **Aembit Client ID**, **Token Lifetime**, and **Absolute Token Lifetime** fields.

   ![The Users tab of an Identity Provider with Enable User Access turned on](https://docs.aembit.io/_astro/aembit-desktop-identity-provider-user-access.DVSSZimW_Z2povs3.webp)

7. Set **Token Lifetime**, how long each access token lasts and how long a session can go without renewing.

   Select **Minutes** or **Hours** as the unit. The default is 24 hours, and the minimum is 5 minutes.

8. Set **Absolute Token Lifetime**, the maximum duration of a session before the person must authenticate again.

   Select **Hour**, **Day**, **Week**, or **Month** as the unit. The default is 30 days, and the maximum is 365 days. The value must be greater than **Token Lifetime**.

9. Copy the **Aembit Client ID** with the copy button beside the field.

   The value identifies this Identity Provider rather than an individual person, and a Client Workload’s Aembit Client ID is a different value. Give it to everyone who signs in to Aembit Desktop through this Identity Provider.

10. Click **Save**.

## Use the Aembit Client ID

People pass the Aembit Client ID to `adt login` when they sign in to Aembit Desktop. You can also deliver it to their machines in a settings file, as in [Configure Aembit Desktop settings](../../desktop/configure.md). Continue with [Set up user access for Aembit Desktop](../../desktop/set-up-user-access.md) to create the Client Workload, Trust Provider, and Access Policy that grant access to a Server Workload.
