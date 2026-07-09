---
type: how-to
title: "GitHub ID Token Subject"
description: "This page describes how the GitHub ID Token Subject method identifies Client Workloads in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/github-id-token-subject/
interface: web-ui
tags: [cicd, identification, client-workload, access-policy]
timestamp: 2026-01-12T11:05:32-08:00
type_inferred: true
---

# GitHub ID Token Subject

This Client Workload identification method is specifically designed for [GitHub Action deployments](../../../deploy-install/ci-cd/github/overview.md).

**The GitHub ID Token Subject** identification method allows you to identify GitHub workflows based on their repository and triggering event. Aembit achieves this using the **subject** claim within the OIDC token issued by GitHub Actions.

## Applicable Deployment Type

[Section titled “Applicable Deployment Type”](#applicable-deployment-type)

This method is suitable for GitHub-based CI/CD Workflow deployments.

## Configuration

[Section titled “Configuration”](#configuration)

### Aembit Cloud

[Section titled “Aembit Cloud”](#aembit-cloud)

1. Create a new Client Workload.
2. Choose **GitHub ID Token Subject** for client identification.
3. Construct a subject manually using the format specified below and use it in the **Value** field.

The GitHub ID Token Subject method provides advanced workflow identification capabilities by allowing you to identify Client Workloads based on repository origin, triggering events (like pull requests), branches, and more. The following example is for a pull request triggered workflow:

* **Format** - repo:`{orgName}/{repoName}`:pull\_request
* **Example** - repo:my-org/my-repo:pull\_request

For more subject claims and examples, refer to the [GitHub OIDC Token Documentation](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#example-subject-claims).

### Finding the GitHub ID Token Subject:

[Section titled “Finding the GitHub ID Token Subject:”](#finding-the-github-id-token-subject)

You can reconstruct subject claim as follows:

1. Identify the repository: Navigate to your project on GitHub. Locate the repository name displayed at the top left corner of the page.
2. Determine filtering criteria: Choose the specific element you want to use for precise workflow selection: a deployment environment (e.g., “production”), a triggering event (e.g., “pull\_request” or “push”), or a specific branch or tag name.
3. Combine the information: Assemble the subject using the format: `repo:{organization}/{repository}:<filtering criteria>`.

Alternatively, you can inspect the GitHub OIDC token to extract the **subject** claim. For further details, please contact Aembit.

## Related

**Compatible trust providers**

* [GitHub Action ID Token](../../trust-providers/github-trust-provider.md)
