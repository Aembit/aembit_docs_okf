---
type: reference
title: "Support matrix"
description: "Supported features for each deployment type"
resource: https://docs.aembit.io/reference/support-matrix/
timestamp: 2026-09-15T20:39:46-07:00
---

# Support matrix

The matrices on this page detail the compatible deployment types for [application protocols](#application-protocols). The matrices also cover Aembit features such as [Client Workload Identifiers](#client-workload-identifiers), [Agent Controller Trust Providers](#agent-controller-trust-providers), [Agent Proxy Trust Providers](#agent-proxy-trust-providers), and [Conditional Access](#conditional-access). The matrices list the [operating systems for VMs](#supported-operating-systems-for-vms) that Aembit supports. The [CLI Support](#cli-support) section includes the operating systems and Access Policy features that the Aembit CLI supports. The [SDK support](#sdk-support) section covers Trust Providers by language.

Aembit Edge supports multiple types of deployments:

* Kubernetes

* AWS Elastic Container Service (ECS) Fargate

* Virtual Machines (Linux, Windows, Docker-compose)

* AWS Lambda (function, container)

> **For Linux Virtual Machines**
>
> Aembit supports Client Workloads running directly on the VM or within Docker-compose on the VM. Aembit collects different data from applications running in Docker-compose compared to those running directly on the VM.

## Key

| Icon | Meaning        |
| ---- | -------------- |
| ✅    | Supported      |
| ❌    | Not supported  |
| ⚪️   | Not applicable |

## Application protocols

| Application Protocols           | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | --------------------- | ---------- |
| HTTP 1.1                        | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |
| Postgres 3.0                    | ✅          | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |
| MySQL 10                        | ✅          | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |
| Redis RESP2                     | ✅          | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |
| Redis RESP3                     | ✅          | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |
| Snowflake SDK (HTTP-based)      | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |
| Snowflake REST API (HTTP-based) | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |
| Amazon Redshift 3.0             | ✅          | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |
| Oracle Database                 | ✅\*        | ❌               | ❌               | ✅                       | ❌                         | ✅                     | ❌          |

> \* *Oracle Database on Kubernetes requires [transparent steering](../user-guide/deploy-install/advanced-options/agent-proxy/selective-transparent-steering.md) configured for the Oracle database host.*

## Client Workload Identifiers

| Client Workload Identifiers | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Docker-compose on VMs | AWS Lambda |
| --------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | --------------------- | ---------- |
| Aembit Client ID            | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |
| AWS Account ID              | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ✅\*                   | ❌          |
| AWS EC2 Instance ID         | ❌          | ⚪️              | ⚪️              | ✅\*                     | ✅\*                       | ✅\*                   | ❌          |
| AWS ECS Task Family         | ⚪️         | ⚪️              | ✅               | ⚪️                      | ⚪️                        | ⚪️                    | ⚪️         |
| AWS Region                  | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ✅\*                   | ❌          |
| AWS Subscription ID         | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ✅\*                   | ❌          |
| AWS VM ID                   | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ✅\*                   | ⚪️         |
| Hostname                    | ❌          | ❌               | ❌               | ✅                       | ✅                         | ✅                     | ❌          |
| Kubernetes Pod name         | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                    | ⚪️         |
| Kubernetes Pod name prefix  | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                    | ⚪️         |
| Process Command Line \*\*   | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                     | ❌          |
| Process Name \*\*           | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                     | ❌          |
| Process Path \*\*           | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                     | ❌          |
| Process User Name \*\*      | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                     | ❌          |
| Source IP                   | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ❌          |
| AWS Lambda ARN              | ⚪️         | ⚪️              | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                    | ✅          |

> \* *These Client Workload identifiers are available for their respective cloud platforms only*.\
> \*\* *Before using process-based identifiers, you must enable them in Agent Proxy first.* *See [Process Command Line](../user-guide/access-policies/client-workloads/identification/process-command-line.md), [Process Name](../user-guide/access-policies/client-workloads/identification/process-name.md), [Process Path](../user-guide/access-policies/client-workloads/identification/process-path.md), and [Process User Name](../user-guide/access-policies/client-workloads/identification/process-user-name.md) for details*

## Agent Controller Trust Providers

| Trust Providers                 | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | --------------- | --------------------- | ---------- |
| AWS Role                        | ❌          | ❌               | ✅               | ❌               | ⚪️                    | ⚪️         |
| AWS Metadata Service            | ✅\*        | ❌               | ❌               | ✅\*             | ⚪️                    | ⚪️         |
| Azure Instance Metadata Service | ✅\*        | ⚪️              | ⚪️              | ✅\*             | ⚪️                    | ⚪️         |
| GCP Identity Token              | ✅\*        | ⚪️              | ⚪️              | ✅\*             | ⚪️                    | ⚪️         |
| Kubernetes Service Account      | ✅          | ✅               | ⚪️              | ⚪️              | ⚪️                    | ⚪️         |
| Kerberos                        | ⚪️         | ⚪️              | ⚪️              | ❌               | ⚪️                    | ⚪️         |

> \* *Aembit tailors the Trust Providers available in Kubernetes and VM environments specifically for their respective cloud platforms*.

## Agent Proxy Trust Providers

| Trust Providers                 | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | --------------------- | ---------- |
| AWS Role                        | ❌          | ❌               | ✅               | ✅\*\*                   | ✅\*\*                     | ❌                     | ✅          |
| AWS Metadata Service            | ✅\*        | ❌               | ❌               | ✅\*                     | ✅\*                       | ✅\*                   | ❌          |
| Azure Instance Metadata Service | ✅\*        | ⚪️              | ⚪️              | ✅\*                     | ✅\*                       | ✅\*                   | ⚪️         |
| GCP Identity Token              | ⚪️         | ⚪️              | ⚪️              | ❌                       | ❌                         | ❌                     | ⚪️         |
| Kubernetes Service Account      | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                    | ⚪️         |
| Kerberos                        | ❌          | ❌               | ❌               | ✅                       | ✅                         | ❌                     | ⚪️         |

> \* *Aembit tailors the Trust Providers available in Kubernetes and VM environments specifically for their respective cloud platforms*.\
> \*\* *The AWS Role Trust Provider supports only EC2 instances with an attached IAM role*.

## Conditional Access

| Access Conditions | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Docker-compose on VMs | AWS Lambda |
| ----------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | --------------------- | ---------- |
| CrowdStrike       | ❌          | ❌               | ❌               | ✅                       | ✅                         | ✅                     | ❌          |
| Wiz               | ✅          | ✅               | ❌               | ❌                       | ❌                         | ❌                     | ✅          |
| Time              | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |
| GeoIP             | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                     | ✅          |

## Supported operating systems for VMs

The following sections contain the operating system versions that Aembit Agent Proxy and Agent Controller support on VMs

### Linux distributions

| Linux Distribution | Version |
| ------------------ | ------- |
| Ubuntu             | 20.04   |
| Ubuntu             | 22.04   |
| Ubuntu             | 24.04   |
| Red Hat            | 8.6     |
| Red Hat            | 8.9     |
| Red Hat            | 9.3     |

### Windows editions

| Windows Edition | Version |
| --------------- | ------- |
| Windows Server  | 2019    |
| Windows Server  | 2022    |

## CLI support

### CLI operating system support

You can use the Aembit CLI with the following operating system versions:

#### Linux distributions

| Linux Distribution | Version |
| ------------------ | ------- |
| Ubuntu             | 22.04   |
| Red Hat            | 9.3     |

#### Windows editions

| Windows Edition | Version |
| --------------- | ------- |
| Windows         | 10      |
| Windows Server  | 2019    |
| Windows Server  | 2022    |

### CLI CI/CD runner support

The Aembit CLI is compatible with the following CI/CD runners:

#### GitHub-hosted runners

For more information, see [GitHub runners documentation](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners).

| GitHub Runner    |
| ---------------- |
| `ubuntu-latest`  |
| `windows-latest` |

#### GitLab-hosted runners

For more information, see [GitLab runners documentation](https://docs.gitlab.com/runner/).

| GitLab Runner               |
| --------------------------- |
| `saas-linux-small-amd64`    |
| `saas-linux-medium-amd64`   |
| `saas-linux-large-amd64`    |
| `saas-linux-small-arm64`    |
| `saas-linux-medium-arm64`   |
| `saas-linux-large-arm64`    |
| `saas-windows-medium-amd64` |

### CLI deployment model support

The Aembit CLI supports the following deployment models:

* [GitHub Actions](../user-guide/deploy-install/ci-cd/github/github-edge-cli.md)
* [GitLab Jobs](../user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-cli.md)
* [Jenkins Pipelines](../user-guide/deploy-install/ci-cd/jenkins-pipelines.md)
* Environments that provide OIDC tokens. See [OIDC ID Token Trust Provider](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) for more info.

### CLI Client Workload Identifiers

The Aembit CLI supports the following Client Workload Identifiers:

* [Aembit Client ID](../user-guide/access-policies/client-workloads/identification/aembit-client-id.md)

### CLI Trust Providers

The Aembit CLI supports the following Trust Providers:

* [AWS Metadata Service Trust Provider](../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role Trust Provider](../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)
* [GitHub Trust Provider](../user-guide/access-policies/trust-providers/github-trust-provider.md)
* [GitLab Trust Provider](../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)
* [Kubernetes Service Account Trust Provider](../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)
* [OIDC ID Token Trust Provider](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md)

### CLI Credential Providers

The Aembit CLI supports the following Credential Providers:

* [Aembit Access Token](../user-guide/access-policies/credential-providers/aembit-access-token.md)
* [API Key](../user-guide/access-policies/credential-providers/api-key.md)
* [AWS Secrets Manager Value](../user-guide/access-policies/credential-providers/aws-secrets-manager.md)
* [AWS Security Token Service Federation](../user-guide/access-policies/credential-providers/aws-security-token-service-federation.md)
* [Azure Entra Workload Identity Federation](../user-guide/access-policies/credential-providers/azure-entra-workload-identity-federation.md)
* [Azure Key Vault Value](../user-guide/access-policies/credential-providers/azure-key-vault.md)
* [Claude Workload Identity Federation](../user-guide/access-policies/credential-providers/claude-workload-identity-federation.md)
* [Google Workload Identity Federation](../user-guide/access-policies/credential-providers/google-workload-identity-federation.md)
* [JSON Web Token (JWT)](../user-guide/access-policies/credential-providers/json-web-token.md)
* [JWT-SVID Token](../user-guide/access-policies/credential-providers/spiffe-jwt-svid.md)
* [Managed GitLab Account](../user-guide/access-policies/credential-providers/managed-gitlab-account.md)
* [OAuth 2.0 Authorization Code](../user-guide/access-policies/credential-providers/oauth-authorization-code.md)
* [OAuth 2.0 Client Credentials](../user-guide/access-policies/credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../user-guide/access-policies/credential-providers/oidc-id-token.md)
* [OpenAI API Workload Identity Federation](../user-guide/access-policies/credential-providers/openai-workload-identity-federation.md)
* [Username & Password](../user-guide/access-policies/credential-providers/username-password.md)
* [Vault Client Token](../user-guide/access-policies/credential-providers/vault-client-token.md)
* [X.509-SVID Certificate](../user-guide/access-policies/credential-providers/spiffe-x509-svid.md), which also requires the [`--client-tls-private-key`](../dev-guide/cli/reference/credentials-get.md#--client-tls-private-key) option

The Model Context Protocol (MCP) Credential Provider [MCP User-Based Access Token](../user-guide/access-policies/credential-providers/mcp-user-based-access-token.md) identifies an end user rather than a workload, so the Aembit CLI can’t retrieve its credentials.

Some of these Credential Providers recognize a fixed set of credential names rather than a name you choose. See [Credential name support](../dev-guide/cli/credential-names.md) for the names they recognize and for how to work around a fixed name.

### CLI Access Conditions

The Aembit CLI supports the following Access Conditions:

* [GeoIP](../user-guide/access-policies/access-conditions/aembit-geoip.md)
* [Time](../user-guide/access-policies/access-conditions/aembit-time-condition.md)
* [CrowdStrike](../user-guide/access-policies/access-conditions/crowdstrike.md)

## SDK support

### Edge SDK Trust Providers

The [Edge SDK](../dev-guide/sdk/edge/overview.md) is available for TypeScript and Python, and the available Trust Providers differ between the two languages. This table lists every Trust Provider type the Edge API accepts and shows SDK support for each by language.

| Trust Providers                                                                                                                     | TypeScript | Python |
| ----------------------------------------------------------------------------------------------------------------------------------- | ---------- | ------ |
| [AWS Metadata Service Trust Provider](../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)              | ✅          | ❌      |
| [AWS Role Trust Provider](../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)                                      | ✅          | ✅      |
| [Azure Instance Metadata Service Trust Provider](../user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider.md) | ✅          | ❌      |
| [GCP Identity Token Trust Provider](../user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider.md)                  | ✅          | ❌      |
| [GitHub Trust Provider](../user-guide/access-policies/trust-providers/github-trust-provider.md)                                          | ✅          | ✅      |
| [GitLab Trust Provider](../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)                                          | ❌          | ✅      |
| [OIDC ID Token Trust Provider](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md)                            | ✅          | ❌      |
| [Terraform Cloud Trust Provider](../user-guide/access-policies/trust-providers/terraform-cloud-identity-token-trust-provider.md)         | ❌          | ✅      |
| [Kubernetes Service Account Trust Provider](../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)  | ❌          | ❌      |
