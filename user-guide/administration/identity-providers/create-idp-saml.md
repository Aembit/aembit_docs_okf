---
type: how-to
title: "How to create a SAML 2.0 Identity Provider"
description: "How to create a SAML (Security Assertion Markup Language) 2.0 Identity Provider for single sign-on (SSO)"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/create-idp-saml/
interface: web-ui
tags: ["identity-provider", "administration"]
timestamp: 2026-10-13T13:28:30-07:00
---

# How to create a SAML 2.0 Identity Provider

Configuring a Security Assertion Markup Language (SAML) 2.0 Identity Provider (IdP) allows you to offer alternate authentication methods for how users sign in to your Aembit Tenant. For example, Single Sign-On (SSO) instead of the default authentication method of an email and password.

When you configure a SAML-capable IdP in your Aembit Tenant, you must enter either your IdP SAML **Metadata URL** or **Metadata XML** information. The SAML 2.0 form also displays the **Service Provider Entity ID** and **Service Provider Single Sign On URL** that you’ll need to configure in your external Identity Provider.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

## Before you start

Before you configure a SAML 2.0 Identity Provider, ensure:

* You have administrator access to both your Aembit Tenant and your Identity Provider.

* You have completed IdP setup first by adding Aembit as a SAML application in your Identity Provider. You’ll need to provide the following Aembit values to your IdP (the SAML 2.0 form displays them):

  * **Service Provider Entity ID**: Aembit’s unique identifier for SAML authentication
  * **Service Provider Single Sign On URL**: The URL where your IdP sends SAML responses

* You have your Identity Provider’s metadata URL or XML file ready.

> **Which metadata format to use**
>
> **Metadata URL**: Best for most Identity Providers (Okta, Azure AD, Google). The URL allows Aembit to automatically update if your IdP certificate rotates.
>
> **Metadata XML**: Use if your Identity Provider doesn’t provide a public URL or for air-gapped environments. You’ll need to manually update the XML file if your IdP certificate changes.
>
> Use Metadata URL when your IdP supports it.

## Configure a SAML 2.0 Identity Provider

If you use Okta, you can add the Aembit integration from the Okta app catalog instead of a custom SAML application. See [How to set up Okta SSO with your Aembit Tenant](okta/set-up-sso.md).

To configure a SAML 2.0 IdP to work with Aembit, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Identity Providers**.

   Aembit displays the **Identity Providers** page with a list of existing Identity Providers.

4. Click **+ New**, revealing the **Identity Provider** pop out menu.

   ![Adding a Identity Provider page](https://docs.aembit.io/_astro/identity-providers-create-saml.o1u8EA0X_17DgcP.webp)

5. On the **Details** tab, fill out the following fields:

   * **Name** - The name of the SAML Identity Provider (for example, Okta SSO).

   * **Description** - A description of the SAML Identity Provider (this is optional).

   * **Identity Provider Type** - Select **SAML 2.0** from the dropdown.

   * Depending on your Identity Provider, enter the Metadata URL in the **Metadata URL** field or upload an XML file of the metadata in the **Metadata XML** field:

     * **Metadata URL** - The URL where Aembit can retrieve SAML metadata for a specific SAML-capable Identity Provider.

     * **Metadata XML** - Some Identity Providers may not provide a publicly accessible Metadata URL. In these cases, Identity Provider configuration may have an option to download the metadata information in XML form.

     > **Note**
     >
     > When you select **SAML 2.0**, Aembit displays the **Service Provider Entity ID** and **Service Provider Single Sign On URL** fields. You should use these values to configure your external Identity Provider.

6. Optionally, in the **Mappings** tab of the **Identity Provider** page you may specify mapping information between group claims configured in your Identity Provider and user roles available in your tenant.

   Adding this information enables automatic user creation based on the information in SAML response messages sent by your Identity Provider.

   See [Configure automatic user creation](automatic-user-creation.md#map-idp-saml-attributes-to-aembit-user-roles) for more information.

7. Click **Save**.

   Aembit displays the newly created SAML IdP listed on the **Identity Provider** page. Now, when your users log in to your Aembit Tenant, the login UI displays the available SAML SSO options similar to the following screenshot:

   ![Updated Login Page With Okta](https://docs.aembit.io/_astro/updated_login_with_sso.fQpE1NbO_Z1ByrG0.webp)

## Testing your SAML SSO setup

Before enabling “Require Single Sign On” for your entire organization, test your SAML SSO configuration:

1. Open an incognito or private browser window.

2. Go to your Aembit Tenant login page.

3. Click your new SSO option (for example, “Okta SSO”).

4. Verify you’re redirected to your Identity Provider’s login page.

5. Log in using your IdP credentials.

6. Verify you’re redirected back to Aembit and successfully logged in.

> **Caution**
>
> Don’t enable “Require Single Sign On” in your [Sign-On Policy](../sign-on-policy/overview.md) until you’ve successfully tested SSO login with at least one user.
>
> Otherwise, issues may lock out users.

## Troubleshooting

The following sections describe common issues you may encounter when setting up SAML SSO and how to resolve them.

### SSO button doesn’t appear on login page

**Solution**: Wait a few moments and refresh the page. If the button still doesn’t appear, verify that you clicked **Save** in the Identity Provider configuration.

### Error after IdP login

**Solution**: Verify that the Service Provider Entity ID and Service Provider Single Sign On URL in your Identity Provider match what Aembit displays. Check both values as even small differences cause authentication to fail.

### IdP certificate rotation causes SSO to stop working

**Solution**: If you’re using Metadata XML, you’ll need to manually update it when your IdP certificate rotates. Consider switching to Metadata URL if your IdP supports it for automatic certificate updates.

## See also

* [Identity Providers overview](overview.md) - Understand how Identity Providers work with Aembit
* [Automatic User Creation](automatic-user-creation.md) - Configure automatic user creation with Identity Providers
* [How to set up Okta SSO with your Aembit Tenant](okta/set-up-sso.md) - Set up Okta SSO with the Aembit integration from the Okta app catalog
* [Sign-On Policy](../sign-on-policy/overview.md) - Configure single sign-on policies including SSO requirements
