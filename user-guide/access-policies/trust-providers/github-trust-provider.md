---
type: reference
title: "GitHub Trust Provider"
description: "This page outlines the steps required to configure the GitHub Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/github-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# GitHub Trust Provider

The GitHub Trust Provider supports attestation of Client Workloads identities in a [GitHub Actions](https://github.com/features/actions) environment.

> **Enterprise Support**
>
> Aembit supports GitHub Cloud but doesn’t support self-hosted GitHub Enterprise Server instances.

The GitHub Trust Provider relies on OIDC (OpenID Connect) tokens issued by GitHub. These tokens contain verifiable information about the workflow, its origin, and the triggering actor.

## Match rules

The following match rules are available for this Trust Provider type:

| Data       | Description                                                                                                                                                                                                                                                                                                                                                                         | Example                                                 |
| ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| actor      | The GitHub account name that initiated the workflow run                                                                                                                                                                                                                                                                                                                             | user123                                                 |
| repository | The repository where the workflow is running. It can be in the format `{organization}/{repository}` for organization-owned repositories or `{account}/{repository}` for user-owned repositories. For additional information about [Repository Ownership](https://docs.github.com/en/repositories/creating-and-managing-repositories/about-repositories#about-repository-ownership). | * MyOrganization/test-project
* user123/another-project |
| workflow   | The name of the GitHub Action workflow. For additional information about [Workflows](https://docs.github.com/en/actions/using-workflows/about-workflows).                                                                                                                                                                                                                           | build-and-test                                          |

For additional information about GitHub ID Token claims, please refer to [GitHub OIDC Token Documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#understanding-the-oidc-token).

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [GitHub ID Token Repository](../client-workloads/identification/github-id-token-repository.md)
* [GitHub ID Token Subject](../client-workloads/identification/github-id-token-subject.md)
