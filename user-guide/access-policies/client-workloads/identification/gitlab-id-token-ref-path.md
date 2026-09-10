---
type: how-to
title: "GitLab ID Token Ref Path"
description: "This page describes how the GitLab ID Token Ref Path method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/gitlab-id-token-ref-path/
interface: web-ui
tags: ["cicd", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# GitLab ID Token Ref Path


This Client Workload identification method is specifically designed for [GitLab Jobs deployments](../../../deploy-install/ci-cd/gitlab/overview.md).

**The GitLab ID Token Ref Path** identification method allows you to identify GitLab jobs based on the triggering branch or tag name. Aembit utilizes the **ref\_path** claim within the OIDC token issued by GitLab.

Combine this method with additional Client Workload identification methods, such as project path for repository identification.

## Applicable Deployment Type

This method is suitable for GitLab-based CI/CD Workflow deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose **GitLab ID Token Ref Path** for client identification.
3. Construct a ref path manually using the format specified below and use it in the **Value** field.

* **Format** - `refs/{type}/{name}`, where `{type}` can be either `heads` for branches or `tags` for tags, and `{name}` is the branch name or tag name used in the reference.
* **Example** - refs/heads/feature-branch-1

### Finding the GitLab ID Token Ref Path:

You can reconstruct ref path claim as follows:

1. Determine ref type: Identify whether the workflow was triggered by a branch (then ref\_type is heads) or a tag (ref\_type is tags).
2. Get the ref: Find the specific branch name (e.g., main) or tag name (e.g., v1.1.5).Check your workflow configuration or, if accessible, the GitLab UI for triggering event details.
3. Combine the information: Assemble the ref path using the format: `refs/{type}/{name}`.

Alternatively, you can inspect the GitLab OIDC token to extract the **ref\_path** claim. For further details, please contact Aembit.

## Related

**Compatible trust providers**

* [GitLab Job ID Token](../../trust-providers/gitlab-trust-provider.md)
