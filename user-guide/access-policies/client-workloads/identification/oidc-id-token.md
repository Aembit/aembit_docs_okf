---
type: how-to
title: "OIDC ID Token"
description: "How to identify workloads using a custom claim from an OIDC ID token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/oidc-id-token/
interface: web-ui
tags: [generic, identification, client-workload, access-policy]
timestamp: 2026-05-01T10:19:23-07:00
type_inferred: true
---

# OIDC ID Token

This page explains how to use the **OIDC ID Token** identifier to match any claim in an OIDC ID token from a standards-compliant identity provider.

## Understanding the OIDC ID Token identifier

[Section titled “Understanding the OIDC ID Token identifier”](#understanding-the-oidc-id-token-identifier)

Unlike the dedicated [Audience](oidc-id-token-audience.md), [Issuer](oidc-id-token-issuer.md), and [Subject](oidc-id-token-subject.md) identifiers, the OIDC ID Token identifier lets you match on any claim in the token. You specify both a **Claim Name** (the JWT claim key, such as `email`) and a **Claim Value** (the expected string the claim must equal). Aembit evaluates the token, locates the claim by name, and checks whether its value matches.

Use this identifier to restrict access based on custom or non-standard token attributes. Examples include a user’s email address, a custom group membership field, or an organizational attribute your identity provider adds to tokens.

Array-valued claims aren’t supported

This identifier matches scalar string values only. If the claim you specify contains an array of values (for example, `"groups": ["engineering", "platform"]`), the match fails. Use a claim that contains a single string value.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Use this identifier when your Client Workload authenticates using an OIDC ID token from a standards-compliant identity provider. The Access Policy must include an [OIDC ID Token Trust Provider](../../trust-providers/oidc-id-token-trust-provider.md) configured to validate tokens from the same identity provider.

Supported environments include:

* CI/CD pipelines (GitLab CI/CD, GitHub Actions, Terraform Cloud)
* Workloads using the [Aembit MCP Identity Gateway](../../../../ai-guide/mcp/identity-gateway/overview.md)
* Any workload that presents a standards-compliant OIDC token to the Aembit Edge Component

## Create a Client Workload with an OIDC ID Token identifier

[Section titled “Create a Client Workload with an OIDC ID Token identifier”](#create-a-client-workload-with-an-oidc-id-token-identifier)

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **OIDC ID Token**.

   For **Claim Name**, enter the JWT claim you want to match on. The field offers `Subject`, `Issuer`, and `Audience` as common suggestions, but accepts any valid claim name.

   For **Claim Value**, enter the expected string value of that claim.

   For example, to match on a user’s email address:

   * **Claim Name**: `email`
   * **Claim Value**: `user@example.com`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Common custom claim examples

[Section titled “Common custom claim examples”](#common-custom-claim-examples)

The following table shows commonly used claim names and example values. The exact claims available depend on your identity provider and its configuration.

| Claim name   | Description                            | Example value                      |
| ------------ | -------------------------------------- | ---------------------------------- |
| `email`      | User’s email address                   | `user@example.com`                 |
| `sub`        | Subject identifier (user or principal) | `user@example.com` or an opaque ID |
| `iss`        | Issuer URL of the identity provider    | `https://your-org.okta.com`        |
| `aud`        | Intended audience of the token         | `https://your-tenant.id.aembit.io` |
| `tenant_id`  | Custom organizational identifier       | `acme-corp`                        |
| `department` | Custom department claim                | `engineering`                      |

Tip

To discover which claims your identity provider includes in its tokens, decode a sample token using a tool such as [jwt.io](https://jwt.io) and review the available fields.

## Related

**Compatible trust providers**

* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
