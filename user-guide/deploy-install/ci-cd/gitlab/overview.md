---
type: explanation
title: "Deploy Aembit Edge with GitLab Jobs"
description: "How to deploy Aembit Edge Components in a CI/CD environment with GitLab Jobs"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/gitlab/
tags: [gitlab, ci-cd, deploy-install]
timestamp: 2025-08-18T17:47:46-07:00
type_inferred: true
---

# Deploy Aembit Edge with GitLab Jobs

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality; however, the steps for each of these options are specific to the deployment option you select.

The following pages provide information about using Aembit Edge in [GitLab Jobs](https://docs.gitlab.com/ee/ci/jobs/):

* [GitLab CI/CD Component](gitlab-jobs-component.md) - Use the Aembit Edge GitLab CI/CD component
* [Aembit CLI](gitlab-jobs-cli.md) - Use the CLI with GitLab CI/CD jobs

When to use the Component vs CLI

Most users should use the Aembit Edge GitLab CI/CD Component, but some specific use cases may require the Aembit CLI directly.

Use the **[GitLab CI/CD Component](gitlab-jobs-component.md)** when you:

* Want automatic CLI download and OIDC token configuration without manual setup steps
* Want simplified job configuration with built-in `allow-failure`, `stage`, and `job-name` options
* Prefer a streamlined, declarative approach that integrates natively with GitLab CI/CD pipelines

Use the **[Aembit Edge CLI](gitlab-jobs-cli.md)** when you:

* Need advanced CLI options or flags not exposed through the component interface
* Want to combine the CLI with other tools in custom scripts or have specific error handling requirements
