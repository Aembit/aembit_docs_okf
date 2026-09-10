---
type: explanation
title: "Securing multicloud access"
description: "How Aembit secures workload access between cloud providers"
resource: https://docs.aembit.io/get-started/use-cases/multicloud/
tags: ["use-case"]
timestamp: 2026-05-18T14:17:19-07:00
---

# Securing multicloud access

Most organizations run workloads across AWS, Azure, and Google Cloud Platform (GCP). When a workload in one cloud needs to access a resource in another, teams face a federation challenge. Each cloud has its own Identity and Access Management (IAM) system, and connecting them requires custom integration code, shared secrets, or complex federation setups.

Multicloud identity isn’t a networking problem. It’s not about connecting AWS to Azure with a VPN or interconnecting cloud networks. It’s about a specific workload in one cloud authenticating to a specific resource in another, the same Client Workload to Server Workload relationship that Aembit secures everywhere else.

Most multicloud environments aren’t planned from the start. Whether through acquisitions, strategic vendor diversification, or teams independently choosing the best tool for their job, most enterprises end up operating across AWS, Azure, GCP, or a combination. The challenge is that each cloud has its own identity system, and they weren’t designed to work together.

> **Terminology note**
>
> The following terms describe the same pattern but for different audiences: workloads in one cloud accessing resources in another
>
> * **Multicloud** resonates with CISOs and strategic decision-makers who think about *multicloud strategy*
> * **Crosscloud** resonates with DevOps engineers who need to wire different systems together

## What Aembit solves

Cloud providers aren’t incentivized to make cross-cloud identity straightforward. Each has built an IAM system optimized for keeping workloads within their own ecosystem. AWS IAM, Azure Managed Identity, and GCP Workload Identity Federation each work well internally. None of them provide a native way to authenticate a workload in one cloud to a resource in another.

Why cloud IAM systems don’t work together

AWS IAM, Azure Entra, and GCP IAM are each designed to authenticate and authorize workloads within their own cloud. They use different identity formats (AWS SigV4, Azure tokens, GCP JWTs), different policy languages, and different trust models. Cross-cloud federation requires custom integration work to translate between these systems—work that cloud providers don’t prioritize because their business model rewards keeping workloads within their ecosystem.

Aembit simplifies cross-cloud access by acting as a vendor-neutral translation layer between cloud identity systems. Aembit provides a single control plane for access policies across all your cloud environments. Define a policy once, and it applies whether the workload runs on AWS, Azure, or GCP. There’s no per-cloud policy duplication and no drift between environments. A workload in AWS can access resources in Azure or GCP without managing cross-cloud IAM federation directly. Each [Access Policy](../concepts/access-policies.md) connects one Client Workload to one Server Workload, regardless of which clouds they run in.

Aembit handles the identity translation:

* [Trust Providers](../concepts/trust-providers.md) verify workload identity using each cloud’s native identity system (AWS IAM, Azure Entra, GCP Workload Identity)
* [Credential Providers](../concepts/credential-providers.md) generate the credentials needed for the target resource in whatever format it requires
* You define policies in one place instead of configuring federation in each cloud’s IAM console

## When multicloud happens

Multicloud environments don’t start with a strategy document. A company acquires a competitor running on Azure while the parent company runs on AWS. A data team adopts GCP BigQuery because it’s the best fit, while production services run on AWS EKS. A security mandate requires geographic redundancy across providers. These are business decisions, not infrastructure failures, but each one adds another identity system to manage.

Often it’s engineering teams and data teams operating in different clouds. Engineering runs production services on AWS while the data team builds analytics pipelines on GCP or Snowflake. Both teams need access to each other’s resources, and each manages credentials independently with no centralized visibility.

### When this applies

This use case applies when your workloads need to authenticate across cloud boundaries. For example, an AWS Lambda calling an Azure SQL database, a GCP Cloud Run service pushing to an S3 bucket, or Kubernetes pods accessing resources in a different cloud. If your workloads stay within a single cloud provider’s ecosystem, native IAM handles this well. Aembit adds value when workloads need to cross those boundaries.

Many Aembit use cases involve multicloud scenarios. A CI/CD pipeline deploying to both AWS and Azure is both a CI/CD use case and a multicloud use case. A service accessing a SaaS API from GCP touches both third-party access and multicloud. This page focuses on the cross-cloud identity challenge specifically. See [CI/CD Pipelines](ci-cd.md), [Database Access](database-access.md), and [Third-Party SaaS Access](third-party-access.md) for use-case-specific guidance that may also apply.

What traditional cross-cloud federation looks like

Without a vendor-neutral layer, teams typically handle cross-cloud access by:

* Storing long-lived credentials in secrets managers (AWS Secrets Manager, Azure Key Vault)
* Building custom integration code to handle authentication for each cloud
* Setting up complex OIDC federation between cloud identity providers
* Managing credential rotation scripts and update procedures

Each approach has security and operational drawbacks, which is why teams describe these as “workarounds” rather than solutions.

## Real example: AWS Lambda accessing Azure Blob Storage

You might be managing this problem today by maintaining separate credentials for each cloud: an AWS access key, an Azure service principal, a GCP service account key. For a single service accessing resources in three clouds, that’s three sets of credentials to provision, rotate, audit, and revoke. Multiply that across dozens of services, and credential management becomes a significant operational and security burden.

To see how this works in practice, consider a common pattern: a service running in one cloud that needs to access a resource in another. Without Aembit, this means provisioning static credentials in the target cloud and distributing them to the source. This creates the kind of long-lived, hard-to-audit secrets that security teams want to remove.

Your organization runs data processing in AWS Lambda but stores results in Azure Blob Storage. Without Aembit, you’d need to:

1. Store Azure credentials in AWS Secrets Manager or Lambda environment variables
2. Manually rotate those credentials and update your Lambda configuration
3. Build custom code to handle Azure authentication

With Aembit, the Lambda function authenticates using its native AWS identity, and Aembit handles the Azure side:

![AWS Lambda accessing Azure Blob Storage through Aembit with cross-cloud identity translation from AWS IAM to Azure Entra token](https://docs.aembit.io/d2/docs/get-started/use-cases/multicloud-0.svg)

The flow works like this:

1. Your Lambda function makes a request to Azure Blob Storage
2. [Aembit Edge](../concepts/aembit-edge.md) (deployed as a Lambda layer) intercepts the request
3. Aembit verifies the Lambda’s identity using the [AWS Role Trust Provider](../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)
4. If the Access Policy allows this Lambda to access this Azure resource, Aembit provisions an Azure token using the [Azure Entra WIF Credential Provider](../../user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md)
5. Aembit injects the Azure token and forwards the request to Blob Storage

The Lambda makes a standard HTTP request to Azure. Aembit handles the cross-cloud authentication transparently.

### Why this matters for multicloud

Each cross-cloud access path gets its own Access Policy. If you have workloads in three clouds accessing resources in three clouds, you create policies for each specific access pattern you need, such as:

* AWS Lambda → Azure Blob Storage
* GCP Cloud Run → AWS S3
* Azure Functions → Snowflake (hosted on AWS)

Workloads authenticate using their native cloud identity, so there are no shared secrets between clouds and no long-lived credentials stored in environment variables or secrets managers.

Each policy specifies exactly which workload can access which resource. You can restrict access by workload attributes like AWS account ID, Azure subscription, or GCP project.

Aembit logs every cross-cloud access request in one place, regardless of which clouds your workloads use.

## Supported cross-cloud patterns

Aembit verifies Client Workload identity using [Trust Providers](../concepts/trust-providers.md). Each cloud platform has a corresponding Trust Provider that validates workloads running in that environment:

* [AWS Role Trust Provider](../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) for EC2, ECS, Lambda, and EKS workloads
* [Azure Instance Metadata Service Trust Provider](../../user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider.md) for VMs, AKS, and Functions workloads
* [GCP Identity Token Trust Provider](../../user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md) for Compute Engine, GKE, and Cloud Run workloads

Aembit can provision credentials for resources in:

* [AWS](../../user-guide/access-policies/credential-providers/aws-security-token-service-federation.md) (S3, DynamoDB, any AWS service)
* [Azure](../../user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md) (Blob Storage, Cosmos DB, any Azure service)
* [GCP](../../user-guide/access-policies/credential-providers/google-workload-identity-federation.md) (Cloud Storage, BigQuery, any GCP service)
* Third-party services like [Snowflake](../../user-guide/access-policies/server-workloads/guides/snowflake.md), [Databricks](../../user-guide/access-policies/server-workloads/guides/databricks.md), and [more](../../user-guide/access-policies/server-workloads/guides/overview.md)

## Next steps

* For securing pipelines that deploy across clouds, see [CI/CD Pipelines](ci-cd.md)
* For cross-cloud database access specifically, see [Database Access](database-access.md)
* For third-party API access from any cloud, see [Third-Party SaaS Access](third-party-access.md)

To configure your first cross-cloud access policy, see [Getting Started with Aembit](../overview.md).
