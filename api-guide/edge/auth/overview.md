---
type: explanation
title: "Edge API authentication methods"
description: "Authenticating with Aembit Edge API using different methods"
resource: https://docs.aembit.io/api-guide/edge/auth/
tags: [auth, edge]
timestamp: 2026-06-16T09:59:40-07:00
type_inferred: true
---

# Edge API authentication methods

Aembit Edge API supports multiple authentication methods to accommodate different environments and workloads, including cloud providers, CI/CD systems, and container orchestration platforms. The authentication process involves attesting the identity of Client Workloads and validating them against your configured Trust Providers. Edge API then issues an access token for subsequent API calls.

## List of authentication methods

[Section titled “List of authentication methods”](#list-of-authentication-methods)

The `/edge/v1/auth` endpoint accepts the following attestation methods. For the full request schema and field-level details, see the [`auth` endpoint reference](../endpoints/auth.md).

### AWS

[Section titled “AWS”](#aws)

* [AWS Metadata Service](aws-metadata-service.md) - Authenticate EC2 instances using the instance identity document and its signature from the AWS Instance Metadata Service (IMDS).
* [AWS IAM Role](aws-iam-role.md) - Authenticate using a signed AWS STS `GetCallerIdentity` request, proving identity through an attached IAM role.
* [AWS Lambda](aws-lambda.md) - Authenticate Lambda functions with a signed STS `GetCallerIdentity` request from the function’s execution role, optionally identified by the function ARN.
* [AWS ECS](aws-ecs.md) - Authenticate ECS tasks with a signed STS `GetCallerIdentity` request from the task’s IAM role, optionally identified by container and task metadata.

### Kubernetes

[Section titled “Kubernetes”](#kubernetes)

* [Kubernetes service account](../endpoints/auth.md#kubernetes-attestation) - Authenticate pods using a Kubernetes service account JWT.

### CI/CD platforms

[Section titled “CI/CD platforms”](#cicd-platforms)

* [GitHub Actions](../endpoints/auth.md#github-actions-attestation) - Authenticate workflows using a GitHub-issued OIDC identity token.
* [Terraform Cloud](../endpoints/auth.md#terraform-cloud-attestation) - Authenticate workspaces using a Terraform Cloud OIDC identity token.
* [GitLab Jobs](../endpoints/auth.md#gitlab-jobs-attestation) - Authenticate CI/CD pipelines using a GitLab-issued OIDC identity token.
