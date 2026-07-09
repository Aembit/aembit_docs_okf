---
type: how-to
title: "GitLab ID Token Subject"
description: "This page describes how the GitLab ID Token Subject method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/gitlab-id-token-subject/
interface: web-ui
tags: [cicd, identification, client-workload, access-policy]
timestamp: 2025-08-18T17:47:46-07:00
type_inferred: true
---

# GitLab ID Token Subject


This Client Workload identification method is specifically designed for [GitLab Jobs deployments](../../../deploy-install/ci-cd/gitlab/overview.md).

**The GitLab ID Token Subject** identification method allows you to identify GitLab jobs based on their group, project, and triggering branch or tag. Aembit achieves this using the **subject** claim within the OIDC token issued by GitLab.

Combine this method with additional Client Workload identification techniques, for project path and reference identification.

## Applicable Deployment Type

[Section titled “Applicable Deployment Type”](#applicable-deployment-type)

This method is suitable for GitLab-based CI/CD Workflow deployments.

## Configuration

[Section titled “Configuration”](#configuration)

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **GitLab ID Token Subject** for client identification.
3. Construct a subject manually using the format specified below and use it in the **Value** field.

* **Format** - `project_path:{group}/{project}:ref_type:{type}:ref:{branch_name}`, where `type` can be either `branch` (for a branch-triggered workflow) or `tag` (for a tag-triggered workflow).
* **Example** - project\_path:my-group/my-project:ref\_type:branch:ref:feature-branch-1

### Finding the GitLab ID Token Subject:

[Section titled “Finding the GitLab ID Token Subject:”](#finding-the-gitlab-id-token-subject)

You can reconstruct subject claim as follows:

1. Identify the project path: Navigate to the **Projects** on GitLab and go to the **All** tab. Locate your project and copy the full displayed project path (e.g., my-group/my-project).
2. Determine ref type: Identify whether the workflow was triggered by a branch (then ref\_type is branch) or a tag (ref\_type is tag).
3. Get the ref: Find the specific branch name (e.g., main) or tag name (e.g., v1.2.0). Check your workflow configuration or, if accessible, the GitLab UI for triggering event details.
4. Combine the information: Assemble the subject using the format: `project_path:{group}/{project}:ref_type:{type}:ref:{branch_name}`.

Alternatively, you can inspect the GitLab OIDC token to extract the **subject** claim. For further details, please contact Aembit.

## Related

**Compatible trust providers**

* [GitLab Job ID Token](../../trust-providers/gitlab-trust-provider.md)
