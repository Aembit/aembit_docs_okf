---
type: how-to
title: "OIDC ID Token Audience"
description: "How to identify workloads using the audience claim from an OIDC ID token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/oidc-id-token-audience/
interface: web-ui
tags: [generic, identification, client-workload, access-policy]
timestamp: 2026-05-01T10:19:23-07:00
type_inferred: true
---

# OIDC ID Token Audience

This page explains how to use the **OIDC ID Token Audience** identifier to identify workloads that present OIDC ID tokens from a standards-compliant identity provider.

## Understanding the OIDC ID Token Audience identifier

[Section titled “Understanding the OIDC ID Token Audience identifier”](#understanding-the-oidc-id-token-audience-identifier)

The `aud` (audience) claim in an OIDC ID token specifies the intended recipient of the token. When Aembit evaluates this identifier, it compares the `aud` claim in the token against the value you configure. The value must match exactly.

This identifier verifies that the identity provider issued the token specifically for your Aembit deployment. For example, in GitLab CI/CD pipelines, you can explicitly set the audience to your Aembit tenant endpoint in the pipeline configuration, then configure Aembit to match on that value. This ensures a token issued for one system can’t authenticate with another.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Use this identifier when your Client Workload authenticates using an OIDC ID token from a standards-compliant identity provider. The Access Policy must include an [OIDC ID Token Trust Provider](../../trust-providers/oidc-id-token-trust-provider.md) configured to validate tokens from the same identity provider.

Supported environments include:

* CI/CD pipelines (GitLab CI/CD, GitHub Actions, Terraform Cloud)
* Workloads using the [Aembit MCP Identity Gateway](../../../../ai-guide/mcp/identity-gateway/overview.md)
* Any workload that presents a standards-compliant OIDC token to the Aembit Edge Component

## Create a Client Workload with an OIDC ID Token Audience identifier

[Section titled “Create a Client Workload with an OIDC ID Token Audience identifier”](#create-a-client-workload-with-an-oidc-id-token-audience-identifier)

1. Log into your Aembit Tenant.

2. In the sidebar, click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **OIDC ID Token Audience**.

   For **Value**, enter the audience value that your identity provider includes in the token for this workload.

   For example: `https://your-tenant.id.aembit.io`

   See [Audience values by identity provider](#audience-values-by-identity-provider) if you’re unsure what value to enter.

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

Client Workload identifier uniqueness

When you identify a Client Workload using a single identifier such as this one in a complex environment, this identifier may not always provide sufficient uniqueness.

To avoid Aembit unintentionally matching something beyond what it’s intended to match, Aembit recommends that you set additional identifiers to compliment this one, creating a Client Workload identity that’s unique across your environment.

See [Using multiple Client Workload identifiers](client-workload-multiple-ids.md) for guidance on combining this identifier with more specific identifiers such as those on the [Client Workload Identifiers overview](overview.md) page, which includes vendor- and technology-specific identifiers.

## Audience values by identity provider

[Section titled “Audience values by identity provider”](#audience-values-by-identity-provider)

How the identity provider sets the `aud` claim depends on your pipeline or workload configuration.

| Identity provider | How the IdP sets the audience                                                   | Notes                                                                                                                                                                                        |
| ----------------- | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| GitLab CI/CD      | Set explicitly in the pipeline using the `aud:` field in the `id_tokens` block  | See [GitLab OIDC audience configuration](#gitlab-cicd)                                                                                                                                       |
| GitHub Actions    | Defaults to the repository owner’s URL; configurable with the `--audience` flag | See the [GitHub Actions OIDC documentation](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/about-security-hardening-with-openid-connect) |
| Terraform Cloud   | Configured in the Terraform Cloud workspace OIDC settings                       | See the [Terraform Cloud OIDC documentation](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/dynamic-provider-credentials/workload-identity-tokens)                          |
| Okta              | Typically the application’s client ID or a configured audience string           | Check your Okta application settings                                                                                                                                                         |
| Generic OIDC      | Varies by provider configuration                                                | Check your identity provider’s token documentation                                                                                                                                           |

### GitLab CI/CD

[Section titled “GitLab CI/CD”](#gitlab-cicd)

In GitLab CI/CD, you define the audience in the pipeline’s `id_tokens` configuration block. The following example sets the audience to an Aembit tenant endpoint:

.gitlab-ci.yml

```yaml
job_name:
  id_tokens:
    GITLAB_OIDC_TOKEN:
      aud: https://your-tenant.id.aembit.io
  script:
    - echo "OIDC token audience configured for Aembit"
```

Configure the Client Workload’s **OIDC ID Token Audience** value to match the `aud` value you set in the pipeline.

## Related

**Compatible trust providers**

* [OIDC ID Token](../../trust-providers/oidc-id-token-trust-provider.md)
