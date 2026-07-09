---
type: how-to
title: "How to create an OIDC 1.0 Identity Provider"
description: "How to create an OIDC 1.0 Identity Provider for Single Sign-On"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/create-idp-oidc/
interface: web-ui
tags: [identity-provider, administration]
timestamp: 2026-04-07T10:38:56-07:00
type_inferred: true
---

# How to create an OIDC 1.0 Identity Provider

Configuring an OIDC 1.0 (OpenID Connect) Identity Provider (IdP) allows you to offer alternate authentication methods for how users sign in to your Aembit Tenant. For example, Single Sign-On (SSO) instead of the default authentication method of an email and password.

When you configure an OIDC-capable IdP in your Aembit Tenant, Aembit provides a redirect URL that you must configure in your third-party IdP.

## Before you start

[Section titled “Before you start”](#before-you-start)

Before you configure an OIDC 1.0 Identity Provider, ensure:

* You have administrator access to both your Aembit Tenant and your Identity Provider.

* You have registered Aembit as an OIDC application in your Identity Provider and have the necessary credentials (such as Client ID and, if applicable, Client Secret) available.

* You have your Identity Provider’s base URL (also called the issuer URL or OIDC discovery endpoint).

* You have your OIDC application’s Client ID from your Identity Provider.

* You have a Teams or Enterprise subscription plan. The Identity Providers feature isn’t available on the Starter plan.

Deciding on an authentication method

When configuring the **Authentication Method** for your OIDC IdP, you have two options:

**Client Secret**: Simpler to set up. Your IdP provides a secret string that Aembit stores securely.

**Public Private Keypair**: More secure. Aembit manages a set of dynamic key pairs and provides a JWKS URL that your IdP can use to fetch the public keys dynamically. Aembit rotates these keys for enhanced security.

Aembit recommends using Public Private Keypair when your IdP supports it.

## Configure an OIDC 1.0 Identity Provider

[Section titled “Configure an OIDC 1.0 Identity Provider”](#configure-an-oidc-10-identity-provider)

To configure an OIDC 1.0 IdP to work with Aembit, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Identity Providers**.

   Aembit displays the **Identity Providers** page with a list of existing Identity Providers.

4. Click **+ New**, revealing the **Identity Provider** pop out menu.

   ![Adding a Identity Provider page](https://docs.aembit.io/_astro/identity-providers-create-oidc.Du0q1TuU_EQjYt.webp)

5. On the **Details** tab, fill out the following fields:

   * **Name** - The name of the OIDC Identity Provider (for example, Okta OIDC or Azure OIDC).

   * **Description** - A description of the OIDC Identity Provider (this is optional).

   * **Identity Provider Type** - Select **OIDC 1.0** from the dropdown.

   * **Identity Provider Base URL** - The OIDC base URL of your Identity Provider. This is typically the issuer URL or OIDC discovery endpoint.

     See [Provider-specific examples](#provider-specific-examples) for guidance on finding this URL of your IdP.

   * **Identity Provider Client ID** - The Client ID from your OIDC application in your Identity Provider.

   * **Identity Provider Scopes** - The OIDC scopes required for authentication.

     Aembit provides a default set of required scopes: `openid profile email`.

     You can add additional scopes, such as `groups`, if your IdP supports them and you want to enable automatic user creation based on group membership. See [Configure automatic user creation](automatic-user-creation.md) for more information.

   * **PKCE Required** - Enable this checkbox to require Proof Key for Code Exchange (PKCE), an additional security layer for OAuth 2.0 flows. Aembit recommends leaving this enabled for enhanced security.

   * **Authentication Method** - Choose between **Client Secret** and **Public Private Keypair**:

     * **Client Secret**: If you select this option, you’ll need to enter the client secret from your OIDC application in the **Identity Provider Client Secret** field.

     * **Public Private Keypair**: If you select this option, Aembit provides a JWKS URL that you must configure in your IdP. Ensure your IdP can access this URL over the internet for key validation. This allows secure communication using JWT signing without requiring a client secret.

6. After entering the Identity Provider details, Aembit displays a **Redirect URL**.

   Copy this URL and configure it in your third-party IdP as the redirect URI or callback URL of your OIDC application.

7. Optionally, in the **Mappings** tab of the **Identity Provider** page you may specify mapping information between group claims configured in your IdP and user roles available in your tenant.

   Adding this information enables automatic user creation based on the information in the OIDC ID token sent by your IdP.

   See [Configure automatic user creation](automatic-user-creation.md) for more information.

8. Click **Save**.

   Aembit displays the newly created OIDC IdP on the **Identity Provider** page. Now, when users log in to your Aembit Tenant, the login UI displays the available OIDC SSO option.

## Provider-specific examples

[Section titled “Provider-specific examples”](#provider-specific-examples)

* Okta

  Follow these steps to configure Okta as an OIDC 1.0 Identity Provider for Aembit.

  1. In Okta, create a new OIDC application or select an existing one.

  2. Copy the **Client ID** from the application’s settings.

  3. For the **Identity Provider Base URL**, use your Okta domain in this format: `https://your-domain.okta.com`

  4. If using **Public Private Keypair**:

     * In Okta, go to your application’s **General Settings**.

     * Set **Client authentication** to **Public key / Private key**.

     * Enable **Require Proof Key for Code Exchange (PKCE)**.

     * Configure the JWKS URL option and paste the JWKS URL provided by Aembit.

  5. If using **Client Secret**:

     * Copy the client secret from your Okta application.

     * Paste it into the **Identity Provider Client Secret** field in Aembit.

  6. In Okta, add the Aembit Redirect URL to your application’s **Sign-in redirect URIs**.

  7. Save your configuration in both Okta and Aembit.

* Azure AD

  Follow these steps to configure Azure AD (Microsoft Entra ID) as an OIDC 1.0 Identity Provider for Aembit.

  1. In Azure Portal, go to **App registrations** and register a new application (or select an existing one).

  2. Copy the **Application (client) ID**.

  3. For the **Identity Provider Base URL**, go to **Endpoints** in your Azure application.

     Copy the portion of the URL that contains your tenant ID. The format is: `https://login.microsoftonline.com/{tenant-id}/v2.0`

  4. If using **Public Private Keypair**:

     * Azure AD supports JWT bearer authentication.

     * Configure the JWKS URL from Aembit in your Azure application settings.

  5. If using **Client Secret**:

     * Go to **Certificates & secrets** in your Azure application.

     * Click **New client secret**.

     * Copy the secret value and paste it into the **Identity Provider Client Secret** field in Aembit.

  6. In Azure, go to your application’s **Authentication** settings.

  7. Under **Platform configurations**, add a **Web** platform if not already present.

  8. Add the Aembit Redirect URL to the **Redirect URIs**.

  9. Click **Configure** to save.

  10. Save your configuration in Aembit.

## Testing your OIDC SSO setup

[Section titled “Testing your OIDC SSO setup”](#testing-your-oidc-sso-setup)

Before enabling “Require Single Sign On” for your entire organization, test your OIDC SSO configuration:

1. Open an incognito or private browser window.

2. Go to your Aembit Tenant login page.

3. Click your new OIDC SSO option (for example, “Okta OIDC” or “Azure OIDC”).

4. Verify you’re redirected to your IdP’s login page.

5. Log in using your IdP credentials.

6. Verify you’re redirected back to Aembit and successfully logged in.

7. Don’t enable “Require Single Sign On” in your Sign-On Policy until you have successfully tested SSO login with at least one user.

Caution

Don’t enable “Require Single Sign On” in your [Sign-On Policy](../sign-on-policy/overview.md) until you’ve successfully tested SSO login with at least one user. Otherwise, Aembit may lock out users who encounter issues.

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

The following sections describe common issues you may encounter when setting up OIDC SSO and how to resolve them.

### SSO button doesn’t appear on login page

[Section titled “SSO button doesn’t appear on login page”](#sso-button-doesnt-appear-on-login-page)

**Solution**: Wait a few moments and refresh the page. If the button still doesn’t appear, verify that you clicked **Save** in the Identity Provider configuration.

### Error after IdP login

[Section titled “Error after IdP login”](#error-after-idp-login)

**Solution**: Verify that the Redirect URL in your IdP exactly matches the Redirect URL provided by Aembit.

### Can’t complete OIDC setup

[Section titled “Can’t complete OIDC setup”](#cant-complete-oidc-setup)

**Solution**: Ensure you have a Teams or Enterprise subscription plan. The Identity Providers feature isn’t available on the Starter plan. Contact Aembit by completing the [Contact Us form](https://aembit.io/contact/) to upgrade your plan.

### Authentication fails with PKCE error

[Section titled “Authentication fails with PKCE error”](#authentication-fails-with-pkce-error)

**Solution**: Ensure that you enable PKCE in both Aembit (PKCE Required checkbox) and your IdP’s OIDC application settings.

### Invalid or expired tokens

[Section titled “Invalid or expired tokens”](#invalid-or-expired-tokens)

**Solution**: If using Public Private Keypair authentication, verify that your IdP can access the JWKS URL provided by Aembit. Check for any firewall or network restrictions that might block this URL.

## See also

[Section titled “See also”](#see-also)

* [Identity Providers overview](overview.md) - Understand how Identity Providers work with Aembit
* [Automatic User Creation](automatic-user-creation.md) - Configure automatic user creation with Identity Providers
* [Sign-On Policy](../sign-on-policy/overview.md) - Configure Sign-On policies including SSO requirements
