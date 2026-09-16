---
type: how-to
title: "OIDC ID Token Subject"
description: "How to identify workloads using the subject claim from an OIDC ID token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/oidc-id-token-subject/
interface: web-ui
tags: ["generic", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-15T18:18:13-07:00
---

# OIDC ID Token Subject

This page explains how to use the **OIDC ID Token Subject** identifier to identify workloads that present OIDC ID tokens from a standards-compliant identity provider.

## Understanding the OIDC ID Token Subject identifier

The `sub` (subject) claim in an OIDC ID token uniquely identifies the principal the token represents—typically a user, a CI/CD pipeline run, or a service account. When Aembit evaluates this identifier, it compares the `sub` claim in the token against the value you configure. The value must match exactly.

The subject format varies by identity provider. For CI/CD systems, it typically encodes the pipeline context such as the repository, branch, or run phase. For user-authenticated flows such as the Aembit MCP Identity Gateway, it typically contains the user’s email address or an opaque user identifier.

## Applicable deployment type

Use this identifier when your Client Workload authenticates using an OIDC ID token from a standards-compliant identity provider. The Access Policy must include an [OIDC ID Token Trust Provider](../../trust-providers/oidc-id-token-trust-provider.md) configured to validate tokens from the same identity provider.

Supported environments include:

* CI/CD pipelines (GitLab CI/CD, GitHub Actions, Terraform Cloud)
* Workloads using the [Aembit MCP Identity Gateway](../../../deploy-install/mcp-identity-gateway/overview.md)
* Any workload that presents a standards-compliant OIDC token to the Aembit Edge Component

> **Note**
>
> For GitLab CI/CD deployments that use Aembit’s dedicated GitLab trust provider instead of the Generic OIDC ID Token Trust Provider, use the [GitLab ID Token Subject](gitlab-id-token-subject.md) identifier instead. The subject value format is identical; the difference is which trust provider type the Access Policy uses.

## Create a Client Workload with an OIDC ID Token Subject identifier

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **OIDC ID Token Subject**.

   For **Value**, enter the subject claim value for your workload.

   For example, for a GitLab CI/CD pipeline on the `main` branch: `project_path:platform-team/api-gateway:ref_type:branch:ref:main`

   See [Subject formats by identity provider](#subject-formats-by-identity-provider) if you’re unsure what value to enter.

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Subject formats by identity provider

The `sub` claim format differs across identity providers. Use the following table as a reference when constructing the value.

| Identity provider | Subject format                                                                 | Example                                                               |
| ----------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------- |
| GitLab CI/CD      | `project_path:{group}/{project}:ref_type:{type}:ref:{branch_or_tag}`           | `project_path:platform-team/api-gateway:ref_type:branch:ref:main`     |
| GitHub Actions    | `repo:{owner}/{repo}:ref:refs/heads/{branch}`                                  | `repo:acme-corp/api-gateway:ref:refs/heads/main`                      |
| Terraform Cloud   | `organization:{org}:project:{project}:workspace:{workspace}:run_phase:{phase}` | `organization:acme:project:default:workspace:api-prod:run_phase:plan` |
| Okta (user auth)  | User email address or opaque user ID                                           | `jwalker@example.com`                                                 |
| Generic OIDC      | Varies by provider configuration                                               | Check your identity provider’s token documentation                    |

> **Tip**
>
> To see the exact subject value your identity provider includes in its tokens, decode a sample token using a tool such as [jwt.io](https://jwt.io) and read the `sub` field.

## Related

**Compatible trust providers**

* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
