---
type: explanation
title: "Identity Providers overview"
description: "Description of what Identity Providers are and how they work in the Aembit UI"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/
tags: [identity-provider, administration]
timestamp: 2026-03-12T14:33:26-07:00
type_inferred: true
---

# Identity Providers overview

This page explains how Identity Providers work with Aembit and when to use them.

* **Ready to set up SSO?** See [Creating SAML 2.0 Identity Providers](create-idp-saml.md) or [Creating OIDC 1.0 Identity Providers](create-idp-oidc.md)
* **Need to configure automatic user creation?** See [Automatic User Creation](automatic-user-creation.md)
* **Just exploring?** Keep reading to understand the concepts

The Identity Providers feature allows you to offer alternate authentication methods when users sign in to your Aembit tenant. The default authentication method is to use an email and password with the option to [enable and require MFA](../sign-on-policy/overview.md#require-multi-factor-authentication-for-native-sign-in).

Requiring your users to remember and manually enter a username and password every time they sign in to your Aembit tenant is tedious, error-prone, and insecure long-term. To improve user experience and security, set up Single Sign-On (SSO). Integrate an external Identity Provider (IdP) such as Okta, Google, or Microsoft Entra ID. Aembit supports both SAML 2.0 and OIDC 1.0 protocols for SSO authentication.

To enforce the exclusive use of SSO and prevent your users from authenticating with their username and password, enable [Require Single Sign On](../sign-on-policy/overview.md#require-single-sign-on).

Tip

The Identity Providers feature is only available on the following subscription plans:

* Teams plan

* Enterprise plan

To enable Identity Providers, please contact Aembit by completing the [Contact Us form](https://aembit.io/contact/).

## SSO overview

[Section titled “SSO overview”](#sso-overview)

SAML 2.0 (Security Assertion Markup Language) is an open standard for cross-domain Single Sign-On (SSO). SSO allows a user to authenticate in one system—the [Identity Provider](#saml-identity-provider)—and gain access to a different system. The [Service Provider](#service-provider) accepts proof of authentication from the IdP.

### SAML Identity Provider

[Section titled “SAML Identity Provider”](#saml-identity-provider)

The SAML Identity Provider (IdP) enables SSO user authentication where Aembit acts as the Service Provider. Common SAML Identity Providers include Okta, Google, Microsoft Entra ID, and many others.

### Service Provider

[Section titled “Service Provider”](#service-provider)

The Service Provider takes this information and implicitly trusts the information given and provides access to the service or resource. The Aembit Service Provider is an example of a resource that accepts external Identity Provider data.

## Aembit SSO authentication process

[Section titled “Aembit SSO authentication process”](#aembit-sso-authentication-process)

The following occurs during the SSO authentication process on your Aembit Tenant:

1. A user selects the option to authenticate through an IdP on the Aembit Tenant login page.

2. Aembit redirects the user to the IdP’s log in page.

3. The IdP prompts the user to authenticate.

4. If the IdP authentication is successful, the IdP redirects the user back to your Aembit Tenant.

5. Aembit logs the user in through the successful SSO authentication.

The following diagram shows the SSO authentication flow:

![SSO authentication flow between user, Aembit, and Identity Provider](https://docs.aembit.io/d2/docs/user-guide/administration/identity-providers/index-0.svg)

## About automatic user creation

[Section titled “About automatic user creation”](#about-automatic-user-creation)

When you enable the automatic user creation feature, Aembit automatically generates new user accounts on your behalf when your users go through the [SSO authenticate process](#aembit-sso-authentication-process). This automation not only saves time and resources by reducing or eliminating the manual effort needed to manage user accounts but also minimizes errors associated with manual account management. Also, this feature provides granular control of what user roles Aembit assigns to new users it creates.

The automatic user creation feature works by extracting certain SAML attributes in the SAML response from the IdP after successful authentication with that IdP. It’s important to know, however, that not all IdPs configure their SAML attributes the same way. Different IdPs use distinct attribute names to pass user group claim information.

To alleviate these inconsistencies, Aembit allows you to map your IdP’s SAML attributes to the user roles available in your Aembit Tenant. See [Configure automatic user creation](automatic-user-creation.md) for details.

### How automatic user creation works

[Section titled “How automatic user creation works”](#how-automatic-user-creation-works)

During the SSO authentication process, when Aembit verifies the authentication response, if no user account exists for that user, Aembit initiates the automatic user creation process.

Aembit requires an email address to uniquely identify users of your Aembit Tenant. If it can, Aembit populates the first and last name of the users it automatically creates. If not, Aembit sets the first and last name to the user’s email address.

Aembit extracts user information from authentication response claims including email, name, and group membership. For technical details about specific claim requirements and attribute names, see [Configure automatic user creation](automatic-user-creation.md).

## Complete setup checklist

[Section titled “Complete setup checklist”](#complete-setup-checklist)

Setting up SSO requires configuration in two places:

1. **Configure Aembit in your IdP** - Add Aembit as an application in your Identity Provider. See your IdP’s documentation for instructions.

2. **Configure your IdP in Aembit** - Add your IdP to Aembit:

   * For SAML 2.0: See [Creating SAML 2.0 Identity Providers](create-idp-saml.md)
   * For OIDC 1.0: See [Creating OIDC 1.0 Identity Providers](create-idp-oidc.md)

3. **(Optional) Configure automatic user creation** - See [Automatic User Creation](automatic-user-creation.md)

4. **Test SSO with a test user before enforcing it** - Verify SSO works before enabling “Require Single Sign On”

## Additional resources

[Section titled “Additional resources”](#additional-resources)

The following pages provide more information about working with Identity Providers:

* [Creating SAML 2.0 Identity Providers](create-idp-saml.md) - Set up SAML 2.0 SSO in your Aembit Tenant
* [Creating OIDC 1.0 Identity Providers](create-idp-oidc.md) - Set up OIDC 1.0 SSO in your Aembit Tenant
* [Automatic User Creation](automatic-user-creation.md) - Configure automatic user creation with Identity Providers
