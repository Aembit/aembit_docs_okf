---
type: explanation
title: "Okta overview"
description: "How the Aembit integration in the Okta Integration Network connects Okta SSO to your Aembit Tenant"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/okta/
tags: ["okta", "identity-provider", "administration"]
timestamp: 2026-10-13T13:28:30-07:00
---

# Okta overview

Aembit is part of the [Okta Integration Network (OIN)](https://www.okta.com/integrations/aembit/), the catalog of prebuilt app integrations that Okta maintains. The integration lets administrators and users sign in to your Aembit Tenant with Okta as the Single Sign-On (SSO) provider. Sign-in works over OpenID Connect (OIDC) 1.0 or Security Assertion Markup Language (SAML) 2.0.

## How the integration differs from a custom Okta application

You add the Aembit integration from the Okta app catalog instead of building a custom application in Okta. In Okta, you enter your Aembit Tenant ID and, for OIDC 1.0, the ID of the Identity Provider you create in your Aembit Tenant. You don’t copy a redirect URL from Aembit into Okta.

To set up the integration, see [How to set up Okta SSO with your Aembit Tenant](set-up-sso.md).

To build a custom Okta application for OIDC 1.0 instead, see the Okta tab under [Provider-specific examples](../create-idp-oidc.md#provider-specific-examples). For SAML 2.0, see [How to create a SAML 2.0 Identity Provider](../create-idp-saml.md).

## See also

* [How to set up Okta SSO with your Aembit Tenant](set-up-sso.md) - Add the Aembit integration in Okta and connect it to your Aembit Tenant
* [Identity Providers overview](../overview.md) - How Identity Providers work in Aembit
