---
type: how-to
title: "GitLab ID Token Project Path"
description: "This page describes how the GitLab ID Token Project Path method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/gitlab-id-token-project-path/
interface: web-ui
tags: [cicd, identification, client-workload, access-policy]
timestamp: 2025-08-18T17:47:46-07:00
type_inferred: true
---

# GitLab ID Token Project Path


This Client Workload identification method is specifically designed for [GitLab Jobs deployments](../../../deploy-install/ci-cd/gitlab/overview.md).

**The GitLab ID Token Project Path** identification method allows you to identify GitLab jobs based on their project location. Aembit utilizes the **project\_path** claim within the OIDC token issued by GitLab.

## Applicable Deployment Type

[Section titled “Applicable Deployment Type”](#applicable-deployment-type)

This method is suitable for GitLab-based CI/CD Workflow deployments.

## Configuration

[Section titled “Configuration”](#configuration)

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **GitLab ID Token Project Path** for client identification.
3. Identify the project where your workflow is located. Copy the full project path and use it in the **Value** field according to the format below.

* **Format** - `{group}/{project}`
* **Example** - my-group/my-project

### Finding the GitLab ID Token Project Path:

[Section titled “Finding the GitLab ID Token Project Path:”](#finding-the-gitlab-id-token-project-path)

* Navigate to the **Projects** on GitLab and go to the **All** tab. Locate your project and copy the full displayed project path in the format specified above.

![GitLab Project Path](https://docs.aembit.io/_astro/gitlab_path.CLUBUd1P_Z1FUUlB.webp)

## Related

**Compatible trust providers**

* [GitLab Job ID Token](../../trust-providers/gitlab-trust-provider.md)
