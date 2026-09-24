---
type: how-to
title: "How to configure Single Sign On automatic user creation"
description: "How to configure SSO automatic user creation through an identity provider"
resource: https://docs.aembit.io/user-guide/administration/identity-providers/automatic-user-creation/
interface: web-ui
tags: ["identity-provider", "administration"]
timestamp: 2026-09-23T16:03:35-07:00
---

# How to configure Single Sign On automatic user creation

[Automatic user creation](overview.md#about-automatic-user-creation) automatically generates new user accounts on your behalf when your users go through the SSO authenticate process. This feature provides granular control of what user roles Aembit assigns to new users it creates. For more details, see [how automatic user creation works](overview.md#how-automatic-user-creation-works).

## Technical: SAML attribute requirements

For SAML 2.0 Identity Providers, Aembit looks for the presence of the following claim elements in the SAML response to create new user accounts:

* A `NameID` element containing the user’s email address. If the `NameID` element isn’t present or the value isn’t a valid email address, Aembit searches for the `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` claim instead. If Aembit finds neither, the automatic user creation process stops.

* Optionally, first name and last name claims, as described in [User name claims](#user-name-claims). Missing name claims don’t stop automatic user creation.

* An `AttributeStatement` element with at least one `Attribute` child element with an attribute value matching the configuration data entered on the **Mapping** tab of the **Identity Provider** page. This match is necessary to determine which roles Aembit assigns to the new user account. If Aembit doesn’t find a matching attribute value, Aembit won’t create the new user account.

## User name claims

Aembit reads a new user’s first and last names from the same claims for SAML 2.0 and OpenID Connect (OIDC) providers. The first and last names can come from different claims. If your Identity Provider sends more than one claim for the same name, Aembit uses only one of them, and which one it uses may change.

Aembit reads the first name from one of the following claims:

* `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`
* `given_name`
* `givenName`
* `http://openid.net/givename`
* `http://openid.net/givenname`

Aembit reads the last name from one of the following claims:

* `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`
* `family_name`
* `familyName`
* `sn`
* `http://openid.net/surname`

If none of a name’s claims has a value, Aembit takes that name from the OIDC `name` claim or the SAML `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` claim, split on whitespace. The first word becomes the first name, and the remaining words become the last name. Aembit uses the user’s email address for any name it can’t resolve from these claims.

## Prerequisites

To enable automatic user creation in your Aembit Tenant, you must have the following:

* A Teams or Enterprise subscription plan.

* Your Identity Provider’s (IdP) SAML group claim information attribute names and values.

## Common IdP attribute names

Different Identity Providers use different attribute names for group claims. The following table lists common SAML attribute names for groups:

| Identity Provider | SAML Attribute Name for Groups                                   |
| ----------------- | ---------------------------------------------------------------- |
| Okta              | `groups`                                                         |
| Azure AD / Entra  | `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` |
| Google Workspace  | `groups`                                                         |
| OneLogin          | `memberOf`                                                       |

Your IdP may use different names. Check your IdP’s SAML configuration or documentation for the correct attribute names.

## Map IdP SAML attributes to Aembit user roles

To map the group information sent from your Identity Provider to the roles available in your tenant, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Identity Providers**.

   Aembit displays the **Identity Providers** page with a list of existing Identity Providers.

4. [Create a new Identity Provider](create-idp-saml.md) or edit an existing one, and then select the **Mappings** tab.

   ![Identity Provider Mappings](https://docs.aembit.io/_astro/identity_providers_mappings.BwOSg0HO_Z1f4kS3.webp)

5. Click **Edit** if not already in edit mode.

6. Click **+ New**, which adds a new row to the table **Role Assignments** table.

7. In the **SAML Attribute Name** column, use the dropdown to select an existing attribute name or click ”+” to add a new one. Make sure the values correspond to the groups defined in your Identity Provider.

8. In the **SAML Attribute Value** column, use the dropdown to select an existing attribute value or click ”+” to add a new one. Make sure the values correspond to the groups defined in your Identity Provider.

   > **Tip**
   >
   > Refer to your Identity Provider’s configuration documentation for the correct attribute names and values.
   >
   > For example, Azure uses the predefined claim name `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`, while other Identity Providers may allow customization of the claim name for group information.

9. In the **Aembit Roles** column, use the dropdown to select one or more Aembit roles.

   ![Aembit Administration Page - Identity Providers Role Mapping](https://docs.aembit.io/_astro/identity_providers_mappings.BwOSg0HO_Z1f4kS3.webp)

10. If needed, repeat the previous four steps.

11. Click **Save**.

## Examples: Mapping attributes to Aembit roles

The following examples show how to map Identity Provider attributes to Aembit roles for both SAML and OIDC protocols.

### SAML attribute mapping example

For SAML Identity Providers like Azure AD, group claims use specific attribute names:

**First mapping:**

* **SAML Attribute Name**: `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`
* **SAML Attribute Value**: `AembitAdmins`
* **Aembit Roles**: Administrator

**Second mapping:**

* **SAML Attribute Name**: `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`
* **SAML Attribute Value**: `AembitViewers`
* **Aembit Roles**: Viewer

This configuration means: Users in the “AembitAdmins” group in Azure AD are automatically created with Administrator role in Aembit. Users in the “AembitViewers” group are automatically created with Viewer role.

### OIDC attribute mapping example

For OIDC Identity Providers like Azure AD with OIDC, attribute names differ from SAML:

**First mapping:**

* **OIDC Claim Name**: `groups`
* **OIDC Claim Value**: `AembitAdmins`
* **Aembit Roles**: Administrator

**Second mapping:**

* **OIDC Claim Name**: `groups`
* **OIDC Claim Value**: `AembitViewers`
* **Aembit Roles**: Viewer

> **SAML vs OIDC attribute differences**
>
> The same Identity Provider often uses different attribute names for SAML and OIDC:
>
> * **Azure AD / Entra**:
>
>   * SAML groups claim: `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`
>   * OIDC groups claim: `groups`
>   * OIDC user principal name: `upn` (often contains email address)
>
> * **Okta**:
>
>   * SAML groups claim: `groups`
>   * OIDC groups claim: `groups`
>
> Always check your IdP’s documentation for the correct claim names for your protocol.

## Understanding automatic user creation

When users authenticate through SSO, the following scenarios can occur:

* **User exists in Aembit already**: SSO works and the user logs in. Aembit doesn’t create a new user.

* **User exists in IdP but not Aembit (auto-creation `on`)**: Aembit creates a new user with roles based on the configured mappings.

* **User exists in IdP but not Aembit (auto-creation `off`)**: Login fails. The user can’t access Aembit.

* **User’s IdP groups don’t match any mappings**: User creation fails. The user can’t log in. You must configure mappings for the user’s groups.

* **User has multiple matching groups**: User gets all corresponding Aembit roles. For example, if a user is in both “AembitAdmins” and “AembitViewers” groups, they receive both Administrator and Viewer roles.
