---
type: how-to
title: "GitLab ID Token Namespace Path"
description: "This page describes how the GitLab ID Token Namespace Path method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/gitlab-id-token-namespace-path/
interface: web-ui
tags: [cicd, identification, client-workload, access-policy]
timestamp: 2025-08-18T17:47:46-07:00
type_inferred: true
---

# GitLab ID Token Namespace Path


This Client Workload identification method is specifically designed for [GitLab Jobs deployments](../../../deploy-install/ci-cd/gitlab/overview.md).

**The GitLab ID Token Namespace Path** identification method allows you to identify GitLab jobs based on their project owner. Aembit utilizes the **namespace\_path** claim within the OIDC token issued by GitLab.

## Applicable Deployment Type

[Section titled “Applicable Deployment Type”](#applicable-deployment-type)

This method is suitable for GitLab-based CI/CD Workflow deployments.

## Configuration

[Section titled “Configuration”](#configuration)

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **GitLab ID Token Namespace Path** for client identification.
3. Determine whether your workflow resides under a GitLab group or your user account. Copy the group name or username and use it in the **Value** field.

* **Format** - The group or username
* **Example** - my-group

### Finding the GitLab ID Token Namespace Path:

[Section titled “Finding the GitLab ID Token Namespace Path:”](#finding-the-gitlab-id-token-namespace-path)

* Navigate to **Projects** on GitLab.
* If the project is group-owned, go to the **All** tab and locate your project. The Namespace Path is displayed before the slash (/) in the project name.
* If the project is user-based, enter your GitLab username in the **Value** field.

![GitLab Namespace Path](https://docs.aembit.io/_astro/gitlab_path.CLUBUd1P_Z1FUUlB.webp)

## Related

**Compatible trust providers**

* [GitLab Job ID Token](../../trust-providers/gitlab-trust-provider.md)
