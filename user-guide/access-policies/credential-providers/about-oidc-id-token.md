---
type: how-to
title: "About the OIDC ID Token Credential Provider"
description: "This page describes the OIDC ID Token Credential Provider and how it works"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/about-oidc-id-token/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-07-07T18:35:05-07:00
type_inferred: true
---

# About the OIDC ID Token Credential Provider

The OIDC ID Token Credential Provider enables secure identity token generation and exchange with third-party services.

By leveraging Aembit’s custom Identity Provider (IdP) capabilities, the OIDC ID Token Credential Provider generates JWT-formatted tokens that you can use with different Workload Identity Federation (WIF) solutions.

The Credential Provider supports:

* Custom claims configuration
* Flexible signing algorithms
* Integration with identity brokers (AWS STS, GCP WIF, Azure WIF, Vault, etc.)

See [Create an OIDC ID Token Credential Provider](oidc-id-token.md) to create one.

## Common use cases

[Section titled “Common use cases”](#common-use-cases)

* **Cloud Provider Access** - Securely access to AWS, GCP, or Azure resources using their respective WIF solutions.
* **Vault Integration** - Authenticate with HashiCorp Vault using OIDC tokens.
* **Custom Service Authentication** - Integrate with any service that supports OIDC/JWT authentication.

## How the OIDC ID Token Credential Provider works

[Section titled “How the OIDC ID Token Credential Provider works”](#how-the-oidc-id-token-credential-provider-works)

1. **Token Generation** - Aembit’s custom IdP generates JWT-formatted OIDC tokens and signs them using your Aembit Tenant-specific keys.

2. **Client identification** - Aembit identifies each IdP client configuration using an Aembit-specific Uniform Resource Name (URN) notation as its `client_id` (for example: `aembit:useast2:1ed42e:identity:oidc-idtoken:2821c459-5541-4a59-9add-d69d5b3ae3db`).

   Custom claims

   If you’re creating custom claims when configuring an OIDC ID Token Credential Provider, don’t use `client_id` as Aembit reserves the value to identify Client Workloads.

3. **Token Exchange** - The Credential Provider requests tokens from Aembit’s IdP and then exchanges these tokens with external identity brokers to obtain service-specific credentials for the workload.

## Configuration options

[Section titled “Configuration options”](#configuration-options)

The following sections detail the configuration options you have for the OIDC ID Token Credential Provider:

### Claims configuration

[Section titled “Claims configuration”](#claims-configuration)

Aembit’s IdP supports dynamic token generation with the following capabilities:

* **Dynamic Claims** - You can specify Claims at token request time, eliminating the need for pre-configuration. Use the syntax `${expression}` to create dynamic values, such as `${oidc.identityToken.decode.payload.user_email}` to extract claims from incoming OIDC tokens.
* **Client Identification** - Aembit identifies each IdP client (such as Aembit Cloud user, Agent Proxy, or Credential Provider-Workload association) using a unique `client_id` value.
* **Token Customization** - Generated tokens follow configurations associated with the specified IdP client, including claims, scopes, and other parameters.
* **OIDC Token Extraction** - Extract claims from OIDC tokens in credential data using the `.decode.payload` command in templates, for example: `${oidc.identityToken.decode.payload.user_login}`.

See the list of [Common OIDC claims](#common-oidc-claims) for more info.

#### Subject configuration options

[Section titled “Subject configuration options”](#subject-configuration-options)

The OIDC ID Token Credential Provider offers two methods for configuring the subject claim in OIDC ID tokens:

* **Dynamic subject** - Aembit’s Credential Provider determines the subject value at runtime by evaluating runtime variables and the requesting workload’s identity. This allows Aembit to adapt to different callers, generating appropriate subject values for each.

  Use dynamic subjects when you need the token’s subject to accurately reflect the identity of the calling entity, or when different workloads should have different subjects in their tokens.

* **Literal subject** - You provide a fixed, predefined string that Aembit uses as the subject claim in all tokens the OIDC ID Token Credential Provider issues.

  Use literal subjects when you’re integrating with a system that expects a specific, unchanging subject value, or when you want to abstract the actual identity of the calling entity.

### Signing configuration

[Section titled “Signing configuration”](#signing-configuration)

Aembit manages signing keys on a per-tenant basis and has the following characteristics:

* uses the signature algorithm that you choose when setting up your IdP client; either **RS256** or **ES256**.
* maintains different sets of keys for each associated signing algorithm.
* makes all keys available via the public JSON Web Key Set (JWKS) interface.

### Refresh token support

[Section titled “Refresh token support”](#refresh-token-support)

The OIDC ID Token Credential Provider supports optional refresh token configuration for [MCP Authorization Server](../../../ai-guide/mcp/authorization-server/overview.md) use cases. When you enable refresh token support, MCP clients can maintain longer-lived sessions through token rotation without completing a new authorization flow.

For details on how refresh tokens work, see [Token refresh](../../../ai-guide/mcp/authorization-server/concepts-mcp-auth-server.md#token-refresh). For configuration steps, see [Create an OIDC ID Token Credential Provider](oidc-id-token.md).

### Identity broker integration

[Section titled “Identity broker integration”](#identity-broker-integration)

The OIDC ID Token Credential Provider supports integration with different identity brokers through configurable options:

* **Endpoint Configuration** -

  * You specify the HTTP/S endpoint URL
  * You configure custom headers as needed

* **Request Formatting** -

  * Aembit formats request bodies as JSON (with XML support planned for future releases)

* **Response Parsing** -

  * The Credential Provider parses JSON responses (with XML support planned for future releases)
  * You can configure cache lifetime management

## Implementation notes

[Section titled “Implementation notes”](#implementation-notes)

* The Credential Provider builds on existing WIF Credential Provider capabilities.
* Current JWKS endpoint implementation aligns with industry standards (AWS EKS, Google APIs, Okta, GitHub), which typically use RS256 algorithms.
* Aembit recommends testing when using with identity brokers that may have specific algorithm requirements.

## Common OIDC claims

[Section titled “Common OIDC claims”](#common-oidc-claims)

The following table describes some common OIDC claims and how to configure them:

| Claim        | Description                                         | Type            | Configuration Examples                                                                                                                                       |
| ------------ | --------------------------------------------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `iss`        | **Issuer** - Identifies Aembit as the OIDC provider | Auto-generated  | Aembit automatically generates this based on your Aembit Tenant, but you can customize it to match external system requirements.                             |
| `sub`        | **Subject** - Unique identifier for the workload    | Dynamic/Literal | **Dynamic**: `${oidc.identityToken.decode.payload.user_login}` **Literal**: `fixed-subject-value`                                                            |
| `aud`        | **Audience** - Intended recipient of the token      | Literal         | Enter the URI or identifier of your target service (for example, `https://sts.amazonaws.com` for AWS, `https://www.googleapis.com/oauth2/v4/token` for GCP). |
| `exp`        | **Expiration** - When the token becomes invalid     | Auto-generated  | Set the **Lifetime** in minutes (for example, `60` for 1 hour).                                                                                              |
| `iat`        | **Issued At** - Token creation time                 | Auto-generated  | Automatically set by Aembit when the token upon issuance.                                                                                                    |
| `nbf`        | **Not Before** - Token validity start time          | Auto-generated  | Automatically set by Aembit when the token upon issuance.                                                                                                    |
| `jti`        | **JWT ID** - Unique token identifier                | Auto-generated  | Automatically generated by Aembit to prevent replay attacks.                                                                                                 |
| `email`      | **Email** - User’s email address                    | Dynamic/Literal | **Dynamic**: `${oidc.identityToken.decode.payload.user_email}` **Literal**: `user@company.com`                                                               |
| `groups`     | **Groups** - User’s group memberships               | Dynamic/Literal | **Dynamic**: `${oidc.identityToken.decode.payload.groups}` **Literal**: `developers,admins`                                                                  |
| `role`       | **Role** - User’s role or permission level          | Dynamic/Literal | **Dynamic**: `${oidc.identityToken.decode.payload.role}` **Literal**: `admin`                                                                                |
| `department` | **Department** - User’s organizational department   | Dynamic/Literal | **Dynamic**: `${oidc.identityToken.decode.payload.department}` **Literal**: `engineering`                                                                    |

Using custom claims

If you’re creating custom claims when configuring an OIDC ID Token Credential Provider, don’t use `client_id` as Aembit reserves the value to identify Client Workloads.
