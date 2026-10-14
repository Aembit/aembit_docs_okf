---
type: how-to
title: "How to set up Okta SSO with your Aembit Tenant"
description: "Set up Okta Single Sign-On (SSO) to your Aembit Tenant with the Aembit integration in the Okta app catalog"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/okta/set-up-sso/
interface: web-ui
tags: ["okta", "identity-provider", "administration"]
timestamp: 2026-10-13T13:28:30-07:00
---

# How to set up Okta SSO with your Aembit Tenant

This page sets up Single Sign-On (SSO) from Okta to your Aembit Tenant with the Aembit integration from the [Okta Integration Network (OIN)](overview.md), over OpenID Connect (OIDC) 1.0 or Security Assertion Markup Language (SAML) 2.0. Users of the MCP Authorization Server can sign in with the same Identity Provider.

To build a custom Okta application instead, see the Okta tab under [Provider-specific examples](../create-idp-oidc.md#provider-specific-examples).

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## Before you start

Before you set up Okta SSO, make sure you have:

* Administrator access to your Okta org.
* Administrator access to your Aembit Tenant.
* Your Aembit Tenant ID, the first part of your Aembit Tenant URL. In `https://abc123.aembit.io`, the Aembit Tenant ID is `abc123`.

## Configure SSO

Setup moves between your Aembit Tenant and the Okta Admin Console in three parts. Select OIDC 1.0 or SAML 2.0 in any part, and the other parts switch to the same protocol.

### Configure your Aembit Tenant

* OIDC 1.0

  The Aembit integration in Okta needs the ID of an Identity Provider in your Aembit Tenant, and Aembit assigns that ID only after you save the Identity Provider. Save it first with placeholder credentials, then replace them with the credentials Okta creates.

  1. Log into your Aembit Tenant.

  2. Click **Administration** in the left sidebar.

  3. At the top, select **Administration ☰ Identity Providers**.

  4. Click **+ New**.

  5. On the **Details** tab, enter a **Name**, such as `Okta OIDC`, and select **OIDC 1.0** as the **Identity Provider Type**.

  6. In **Identity Provider Base URL**, enter your Okta org URL, such as `https://example.okta.com`.

     To find it, click your username in the upper-right corner of the Okta Admin Console. Okta shows the URL without `https://`, so add it.

  7. In **Identity Provider Client ID**, enter a placeholder, such as `placeholder`.

  8. Leave **Identity Provider Scopes** set to `openid profile email`.

  9. Under **Authentication Method**, select **Client Secret**, and enter a placeholder in **Client Secret**.

  10. Click **Save**.

  11. Click the new Identity Provider to open it, and copy the value of its **ID** field.

      ![Identity Provider details in an Aembit Tenant for a saved OIDC 1.0 Identity Provider, showing its ID field](https://docs.aembit.io/_astro/okta-oin-aembit-oidc-id.Bt28i6oV_1qaeTC.webp)

  > **Note**
  >
  > Leave this browser window open and stay signed in to your Aembit Tenant, because you return to this Identity Provider after you configure Okta. Open the Okta Admin Console in a new window or tab.

* SAML 2.0

  SAML 2.0 setup starts in Okta, because the Identity Provider in your Aembit Tenant needs the metadata URL that the Aembit integration in Okta creates. Continue to [Configure the Okta Admin Console](#configure-the-okta-admin-console).

### Configure the Okta admin console

* OIDC 1.0

  1. In the Okta Admin Console, go to **Applications and Resources** > **Applications** and click **Browse App Catalog**.

  2. Search for `Aembit`, select the Aembit integration, and click **Add Integration**.

     ![Okta app catalog page for the Aembit integration, listing SAML and OIDC, with the Add Integration button](https://docs.aembit.io/_astro/okta-oin-catalog.W054jdY3_ZYxgIS.webp)

  3. On the **General Settings** tab, enter an **Application label** and your Aembit Tenant ID in **Tenant ID**, then click **Next**.

     ![Okta General Settings tab with the Application label and Tenant ID fields](https://docs.aembit.io/_astro/okta-oin-general-settings.zRu7ffGv_Z1dP0X9.webp)

  4. On the **Sign-On Options** tab, select **OpenID Connect**.

  5. In **OIDC IdP ID**, paste the ID you copied from Aembit, then click **Done**.

     ![Okta Sign-On Options tab with OpenID Connect selected and the OIDC IdP ID field](https://docs.aembit.io/_astro/okta-oin-oidc-sign-on-options.BXJaaEYn_ZdG5A8.webp)

  6. On the **Assignments** tab that Okta opens, assign the users or groups who sign in to your Aembit Tenant.

  7. On the app’s **Sign On** tab, copy the **Client ID** and **Client secret**.

     ![Okta Sign On tab showing the Client ID and the masked Client secret](https://docs.aembit.io/_astro/okta-oin-oidc-client-credentials.CmjTEoPW_Z215aDy.webp)

* SAML 2.0

  1. In the Okta Admin Console, go to **Applications and Resources** > **Applications** and click **Browse App Catalog**.

  2. Search for `Aembit`, select the Aembit integration, and click **Add Integration**.

  3. On the **General Settings** tab, enter an **Application label** and your Aembit Tenant ID in **Tenant ID**, then click **Next**.

  4. On the **Sign-On Options** tab, select **SAML 2.0** and copy the **Metadata URL**.

     ![Okta Sign-On Options tab with SAML 2.0 selected and the Metadata URL under Metadata details](https://docs.aembit.io/_astro/okta-oin-saml-sign-on-options.CpcpzzjJ_3tqmo.webp)

  5. Click **Done**.

  6. On the **Assignments** tab that Okta opens, assign the users or groups who sign in to your Aembit Tenant.

### Complete setup in your Aembit Tenant

* OIDC 1.0

  1. While still in your Aembit Tenant, click **Edit** on the Identity Provider you saved earlier.

  2. Replace the placeholders in **Identity Provider Client ID** and **Client Secret** with the values from Okta, then click **Save**.

* SAML 2.0

  1. Log into your Aembit Tenant.

  2. Click **Administration** in the left sidebar.

  3. At the top, select **Administration ☰ Identity Providers**.

  4. Click **+ New**.

  5. On the **Details** tab, enter a **Name**, such as `Okta SAML`, and select **SAML 2.0** as the **Identity Provider Type**.

  6. Paste the Okta metadata URL into **Metadata URL**, then click **Save**.

To create Aembit user accounts for assigned users the first time they sign in, see [Configure automatic user creation](../automatic-user-creation.md).

## Test SSO sign-in

Test the setup with one user before you require everyone to sign in to your Aembit Tenant with SSO.

> **Caution**
>
> Don’t enable “Require Single Sign On” in your [Sign-On Policy](../../sign-on-policy/overview.md) until you’ve successfully tested SSO login with at least one user. Otherwise, Aembit may lock out users who encounter issues.

1. In an incognito or private browser window, go to `https://<tenantId>.aembit.io/login`.

2. Click **Sign In with** followed by your Identity Provider’s name, such as **Sign In with Okta OIDC**.

   ![Aembit Tenant sign-in page with the Sign In with Email and Sign In with Okta OIDC buttons](https://docs.aembit.io/_astro/okta-oin-sign-in.DCLQd-4O_Z1SVEEJ.webp)

3. Sign in to Okta.

   Okta redirects you back to your Aembit Tenant, signed in.

## Troubleshooting

The following sections describe common issues with Okta SSO through the Aembit integration and how to resolve them.

### SSO button doesn’t appear on login page

**Solution**: Wait a few moments and refresh the page. If the button still doesn’t appear, verify that you clicked **Save** in the Identity Provider configuration.

### OIDC sign-in returns to the sign-in page with “SSO challenge failed”

Aembit shows this error on your Aembit Tenant sign-in page before Okta asks the user to sign in.

**Solution**: Open the Identity Provider in your Aembit Tenant. Confirm that **Identity Provider Client ID** and **Client Secret** hold the values from the app’s **Sign On** tab in Okta, not the placeholders. If you saved the values in the last few minutes, wait a few minutes and try again.

### Browser can’t find the Aembit address after SAML sign-in

After the user signs in to Okta, the browser reports that it can’t find an `aembit.io` address that doesn’t match your Aembit Tenant URL. Okta builds that address from the app’s **Tenant ID**.

**Solution**: In Okta, on the app’s **General** tab, click **Edit** under **App Settings**, set **Tenant ID** to your Aembit Tenant ID, and click **Save**.

## See also

* [Okta overview](overview.md) - What the Aembit integration in the Okta Integration Network provides
* [How to create an OIDC 1.0 Identity Provider](../create-idp-oidc.md) - Connect a custom Okta OIDC application or another OIDC provider
* [How to create a SAML 2.0 Identity Provider](../create-idp-saml.md) - Connect another SAML 2.0 provider
* [Automatic User Creation](../automatic-user-creation.md) - Create Aembit users when they first sign in through an Identity Provider
* [Sign-On Policy](../../sign-on-policy/overview.md) - Require users to sign in with SSO
