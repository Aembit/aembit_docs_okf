---
type: how-to
title: "OIDC ID Token Issuer"
description: "How to identify workloads using the issuer claim from an OIDC ID token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/oidc-id-token-issuer/
interface: web-ui
tags: ["generic", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# OIDC ID Token Issuer

This page explains how to use the **OIDC ID Token Issuer** identifier to identify workloads that present OIDC ID tokens issued by a standards-compliant identity provider.

## Understanding the OIDC ID Token Issuer identifier

The `iss` (issuer) claim in an OIDC ID token identifies the identity provider that minted the token. When Aembit evaluates this identifier, it compares the `iss` claim in the token against the value you configure. The value must match exactly.

This identifier is useful for scoping access to workloads that authenticate through a specific identity provider. For example, you can ensure only pipelines from GitLab.com—not a self-hosted instance—can match a Client Workload. Combine the OIDC ID Token Issuer identifier with the Subject or Audience identifiers to narrow the scope further.

## Applicable deployment type

Use this identifier when your Client Workload authenticates using an OIDC ID token from a standards-compliant identity provider. The Access Policy must include an [OIDC ID Token Trust Provider](../../trust-providers/oidc-id-token-trust-provider.md) configured to validate tokens from the same identity provider.

Supported environments include:

* CI/CD pipelines (GitLab CI/CD, GitHub Actions, Terraform Cloud)
* Workloads using the [Aembit MCP Identity Gateway](../../../../ai-guide/mcp/identity-gateway/overview.md)
* Any workload that presents a standards-compliant OIDC token to the Aembit Edge Component

## Create a Client Workload with an OIDC ID Token Issuer identifier

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **OIDC ID Token Issuer**.

   For **Value**, enter the issuer URL of the identity provider that issues tokens for this workload.

   For example: `https://gitlab.com`

   See [OIDC issuer values by identity provider](#oidc-issuer-values-by-identity-provider) if you’re unsure what value to enter.

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

> **Client Workload identifier uniqueness**
>
> When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.
>
> To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.
>
> See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## OIDC issuer values by identity provider

The following table lists issuer values for common identity providers. The value must exactly match the `iss` claim in the token your workload presents.

| Identity provider          | Issuer value                                  |
| -------------------------- | --------------------------------------------- |
| GitLab.com                 | `https://gitlab.com`                          |
| GitHub Actions             | `https://token.actions.githubusercontent.com` |
| Okta                       | `https://your-org.okta.com`                   |
| Terraform Cloud            | `https://app.terraform.io`                    |
| Google (Workload Identity) | `https://accounts.google.com`                 |

> **Tip**
>
> Most OIDC identity providers publish their issuer URL in a discovery document at `{issuer}/.well-known/openid-configuration`. Check the `issuer` field in that document to confirm the exact value for your environment.

## Related

**Compatible trust providers**

* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
