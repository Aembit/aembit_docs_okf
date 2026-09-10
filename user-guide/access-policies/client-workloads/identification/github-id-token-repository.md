---
type: how-to
title: "GitHub ID Token Repository"
description: "This page describes how the GitHub ID Token Repository method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/github-id-token-repository/
interface: web-ui
tags: ["cicd", "identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# GitHub ID Token Repository

This Client Workload identification method is specifically designed for [GitHub Action deployments](../../../deploy-install/ci-cd/github/overview.md).

**The GitHub ID Token Repository** identification method allows you to identify GitHub workflows based on their repository origin. Aembit achieves this using the **repository** claim within the OIDC token issued by GitHub Actions.

## Applicable Deployment Type

This method is suitable for GitHub-based CI/CD Workflow deployments.

## Configuration

### Aembit Cloud

1. Create a new Client Workload.
2. Choose **GitHub ID Token Repository** for client identification.
3. Identify the repository where your workflow is located. Copy this full repository name and use it in the **Value** field according to the format below.

* **Format** - `{organization}/{repository}` for organization-owned repositories or `{account}/{repository}` for user-owned repositories.
* **Example** - user123/another-project

### Finding the GitHub ID Token Repository:

* Navigate to your project on GitHub.
* Locate the repository name displayed at the top left corner of the page, in the format mentioned above.

![Repository name on GitHub](https://docs.aembit.io/_astro/github_repository.DAzhQK9n_Z218s3X.webp)

## Related

**Compatible trust providers**

* [GitHub Action ID Token](../../trust-providers/github-trust-provider.md)
