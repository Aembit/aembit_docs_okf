---
type: how-to
title: "Gitlab Trust Provider"
description: "This page outlines the steps required to configure the Gitlab Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/gitlab-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2025-09-05T06:12:10-07:00
type_inferred: true
---

# Gitlab Trust Provider

The Gitlab Trust Provider supports attestation of Client Workloads identities in a [Gitlab Jobs](https://docs.gitlab.com/ee/ci/jobs/) environment.

Enterprise Support

Aembit supports GitLab Cloud but doesn’t support self-hosted GitLab instances.

The GitLab Trust Provider relies OIDC (OpenID Connect) tokens issued by GitLab. These tokens contain verifiable information about the job, its origin within the project, and the associated pipeline.

## Match rules

[Section titled “Match rules”](#match-rules)

The following match rules are available for this Trust Provider type:

| Data            | Description                                                                                                                                                                                                                                                           | Example                                                                                                                                |
| --------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| namespace\_path | The group or user namespace (by path) where the repository resides.                                                                                                                                                                                                   | my-group                                                                                                                               |
| project\_path   | The repository from where the workflow is running, using the format `{group}/{project}`                                                                                                                                                                               | my-group/my-project                                                                                                                    |
| ref\_path       | The fully qualified reference (branch or tag) that triggered the job. ([Introduced](https://gitlab.com/gitlab-org/gitlab/-/merge_requests/119075) in GitLab 16.0.)                                                                                                    | * refs/heads/feature-branch-1
* refs/tags/v1.2.0                                                                                       |
| subject         | The repository and Git reference from where the workflow is running. The format is `project_path:{group}/{project}:ref_type:{type}:ref:{branch_name}`, where `type` can be either `branch` (for a branch-triggered workflow) or `tag` (for a tag-triggered workflow). | - project\_path:my-group/my-project:ref\_type:branch:ref:feature-branch-1
- project\_path:my-group/my-project:ref\_type:tag:ref:v2.0.1 |

For additional information about GitLab ID Token claims, please refer to [GitLab Token Payload](https://docs.gitlab.com/ee/ci/secrets/id_token_authentication.html#token-payload).

Note

When using GitLab Dedicated, ensure the OIDC Endpoint is properly configured; otherwise use `https://gitlab.com`.

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

* [GitLab ID Token Namespace Path](../client-workloads/identification/gitlab-id-token-namespace-path.md)
* [GitLab ID Token Project Path](../client-workloads/identification/gitlab-id-token-project-path.md)
* [GitLab ID Token Ref Path](../client-workloads/identification/gitlab-id-token-ref-path.md)
* [GitLab ID Token Subject](../client-workloads/identification/gitlab-id-token-subject.md)
