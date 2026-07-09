---
type: how-to
title: "Configure an OpenAI WIF Credential Provider"
description: "Configure an OpenAI Workload Identity Federation Credential Provider so workloads call the OpenAI API with short-lived tokens."
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/openai-workload-identity-federation/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-07-07T15:50:08-04:00
type_inferred: true
---

# Configure an OpenAI WIF Credential Provider

Aembit’s Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) for OpenAI Workload Identity Federation (WIF) lets your Client Workloads**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md) authenticate to the OpenAI API with short-lived tokens instead of long-lived API keys. Aembit obtains those tokens on the Client Workload’s behalf. Aembit acts as a third-party federated Identity Provider (IdP), and OpenAI trusts Aembit as an OIDC**OpenID Connect (OIDC)**: An identity layer built on top of OAuth 2.0 that lets applications verify the identity of a user or workload and obtain basic profile information using JSON Web Tokens (JWTs).[Learn more(opens in new tab)](https://openid.net/developers/how-connect-works/) issuer. When an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) grants a Client Workload access, Aembit exchanges its issued assertion for a short-lived OpenAI access token.

You can configure the OpenAI Credential Provider using the [Aembit Tenant](#create-a-credential-provider) or through the [Aembit Terraform provider](#configure-openai-wif-using-the-aembit-terraform-provider).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To configure an OpenAI WIF Credential Provider, you’ll need the following:

* Ability to access and manage your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../get-started/concepts/administration.md).

* An OpenAI organization owner role, which OpenAI requires to configure Workload Identity Federation. You create a Workload Identity Provider and a service account mapping in your OpenAI organization. See the OpenAI [Workload Identity Federation](https://developers.openai.com/api/docs/guides/workload-identity-federation) documentation.

* Your Aembit OIDC Issuer URL is publicly reachable over HTTPS (port 443). OpenAI performs OIDC discovery and JWKS retrieval against this URL.

* Terraform only:

  * You have Terraform installed.
  * You have the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest) configured.

## Configure a Credential Provider for OpenAI

[Section titled “Configure a Credential Provider for OpenAI”](#configure-a-credential-provider-for-openai)

This section explains how to configure an OpenAI WIF Credential Provider in the Aembit Tenant. You create the Credential Provider in your Aembit Tenant, configure the matching federation trust in OpenAI, then return to your Aembit Tenant to finish and verify.

Use multiple browser tabs/windows

Keep OpenAI open in another browser window alongside the Aembit Tenant so you can work in both spaces.

## Create a Credential Provider

[Section titled “Create a Credential Provider”](#create-a-credential-provider)

1. Log in to your Aembit Tenant and click **Credential Providers**.

2. Click **+ New**, which reveals the **Credential Provider** page.

3. Enter a **Name** and optional **Description**.

4. In the **Credential Type** dropdown, select **OpenAI API Workload Identity Federation**, revealing new fields.

5. Copy the **OIDC Issuer URL**.

   Aembit auto-generates this read-only value as a dedicated endpoint for OIDC authentication with OpenAI. You’ll use it when you register Aembit as a federation issuer in OpenAI in the next section.

   Keep the Aembit Tenant open while you configure the federation trust in OpenAI.

## Configure the federation trust in OpenAI

[Section titled “Configure the federation trust in OpenAI”](#configure-the-federation-trust-in-openai)

In OpenAI, create a **Workload Identity Provider** that trusts Aembit as the OIDC issuer. Then add a **service account mapping** that authorizes Aembit’s tokens to mint access tokens for an OpenAI service account.

Follow OpenAI’s [Workload Identity Federation](https://developers.openai.com/api/docs/guides/workload-identity-federation) documentation to complete this setup, with a few things to note for Aembit:

* Set the provider’s **OIDC Issuer URL** to the Aembit **OIDC Issuer URL** you copied in the previous section. Keep the default key source of OIDC discovery. Aembit’s OIDC Issuer URL serves the `.well-known/openid-configuration` endpoint that OpenAI uses for discovery, so you don’t need to upload a JWKS.
* If you leave the provider’s **Audience** blank, OpenAI defaults it to `https://api.openai.com`. The Aembit Credential Provider screen applies the same default, so the two values match. If you set a custom **Audience**, enter the same value in Aembit in the next section.
* In the service account mapping, match the `sub` claim that Aembit’s token presents, set to `workloadId:*`. OpenAI issues a token only when exactly one enabled mapping matches. It rejects the exchange when no mapping matches or when more than one does, so keep your mappings unambiguous.

As you configure OpenAI, record the following values, which you’ll enter in Aembit in the next section:

1. **Workload Identity Provider ID** - OpenAI generates this when you create the Workload Identity Provider; its ID starts with `idp_`.
2. **Service Account ID** - the OpenAI service account your mapping authorizes; its ID starts with `user-`.

## Complete the Credential Provider in the Aembit Tenant

[Section titled “Complete the Credential Provider in the Aembit Tenant”](#complete-the-credential-provider-in-the-aembit-tenant)

Return to the Aembit Tenant and complete the **Credential Provider** page with the values from OpenAI. For a field-by-field reference, see [OpenAI and Credential Provider UI value mappings](#openai-and-credential-provider-ui-value-mappings).

1. For **Identity Provider ID**, enter the Workload Identity Provider you recorded from OpenAI; its ID starts with `idp_`.

2. For **Service Account ID**, enter the service account you recorded from OpenAI; its ID starts with `user-`.

3. For **Audience**, enter the same **Audience** value you set on the Workload Identity Provider in OpenAI.

   Leave this field blank to use the default `https://api.openai.com`, which matches OpenAI’s default when you also leave its **Audience** blank.

   ![A completed OpenAI Workload Identity Federation Credential Provider in the Aembit Tenant.](https://docs.aembit.io/_astro/credential_providers_openai_wif_dialog_window_completed.D-e8Xtlt_Z6pksI.webp)

4. Click **Save**.

   Your OpenAI WIF Credential Provider now displays in your list of Credential Providers in the Aembit Tenant.

## Verify the connection

[Section titled “Verify the connection”](#verify-the-connection)

To verify the connection between your Aembit Credential Provider and your OpenAI federation setup:

1. On the **Credential Providers** page, select the Credential Provider you just created.

2. Click **Verify**.

   After a few moments, you should see a green banner display a “Verified Successfully” message.

   If verification fails, recheck the values in your Aembit Credential Provider against the Workload Identity Provider, service account, and mapping in OpenAI. A failure often points to an issuer or audience mismatch, or to a mapping that matches no token or more than one token.

You’re now ready to use your Credential Provider for OpenAI Workload Identity Federation with your Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md) in an Aembit [Access Policy](../create-access-policy.md). To configure OpenAI as a Server Workload, see the [ChatGPT (OpenAI)](../server-workloads/guides/openai.md) guide.

## Configure OpenAI WIF using the Aembit Terraform provider

[Section titled “Configure OpenAI WIF using the Aembit Terraform provider”](#configure-openai-wif-using-the-aembit-terraform-provider)

To configure an OpenAI WIF Credential Provider using the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest), follow the steps in this section.

The following configuration is an example. For the authoritative, current argument reference, including required and optional fields, default values, and any new arguments, see the [`aembit_credential_provider` resource](https://registry.terraform.io/providers/Aembit/aembit/latest/docs/resources/credential_provider) in the Terraform Registry.

1. Create a new Terraform configuration file (such as `openai-wif.tf`) following this example structure.

   ```hcl
   provider "aembit" {
   }


   resource "aembit_credential_provider" "openaiWif" {
     name      = "<Desired Credential Provider Name>"
     is_active = true
     openai_wif = {
       identity_provider_id = "<Workload Identity Provider ID from OpenAI>"
       service_account_id   = "<Service Account ID from OpenAI>"
       audience             = "<Optional audience your mapping expects>"
     }
   }
   ```

2. Apply the Terraform configuration:

   ```shell
   terraform apply
   ```

3. Copy the **OIDC Issuer URL** for the new Credential Provider from the Aembit Tenant, then use it in OpenAI when you register Aembit as a Workload Identity Provider.

You’re now ready to use your Credential Provider for OpenAI Workload Identity Federation with your Server Workloads in an Aembit Access Policy.

## OpenAI and Credential Provider UI value mappings

[Section titled “OpenAI and Credential Provider UI value mappings”](#openai-and-credential-provider-ui-value-mappings)

The following table shows how the values in your OpenAI WIF configuration map to the Aembit Credential Provider web UI and Terraform provider:

| Aembit Credential Provider value | OpenAI WIF configuration                       | Terraform value            |
| -------------------------------- | ---------------------------------------------- | -------------------------- |
| OIDC Issuer URL                  | Workload Identity Provider **OIDC Issuer URL** | Not exposed (copy from UI) |
| Identity Provider ID             | Workload Identity Provider ID (`idp_`)         | `identity_provider_id`     |
| Service Account ID               | Mapping **Service account** (`user-`)          | `service_account_id`       |
| Audience                         | Workload Identity Provider **Audience**        | `audience`                 |

## Limitations

[Section titled “Limitations”](#limitations)

* **No Admin API access.** You can’t use OpenAI WIF access tokens to call OpenAI Admin API endpoints.
* **Token lifetime.** OpenAI access tokens expire after at most one hour and can’t outlive the Aembit-issued token used for the exchange.
* **OpenAI organization limits.** OpenAI allows up to 50 Workload Identity Providers per organization and up to 50 service account mappings per provider.
