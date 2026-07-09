---
type: reference
title: "Support matrix"
description: "Supported features for each deployment type"
resource: https://docs.aembit.io/reference/support-matrix/
timestamp: 2026-06-11T11:10:32-04:00
type_inferred: true
---

# Support matrix

The matrices on this page detail the compatible deployment types for [application protocols](#application-protocols) and Aembit features such as [Client Workload Identifiers](#client-workload-identifiers), [Agent Controller Trust Providers](#agent-controller-trust-providers), [Agent Proxy Trust Providers](#agent-proxy-trust-providers), [Conditional Access](#conditional-access) and the [operating systems for VMs](#supported-operating-systems-for-vms) that Aembit supports. Also, the [CLI Support](#cli-support) section includes what operating systems and Access Policy features that the Aembit CLI supports.

Aembit Edge supports multiple types of deployments:

* Kubernetes

* AWS Elastic Container Service (ECS) Fargate

* Virtual Machines (Linux, Windows, Docker-compose)

* AWS Lambda (function, container)

* Virtual Appliance (VMware)

For Linux Virtual Machines

Aembit supports Client Workloads running directly on the VM or within Docker-compose on the VM. Aembit collects different data from applications running in Docker-compose compared to those running directly on the VM.

## Key

[Section titled “Key”](#key)

| Icon | Meaning        |
| ---- | -------------- |
| ✅    | Supported      |
| ❌    | Not supported  |
| ⚪️   | Not applicable |

## Application protocols

[Section titled “Application protocols”](#application-protocols)

| Application Protocols           | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Virtual Appliance | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | ----------------- | --------------------- | ---------- |
| HTTP 1.1                        | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ✅          |
| Postgres 3.0                    | ✅          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |
| MySQL 10                        | ✅          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |
| Redis RESP2                     | ✅          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |
| Redis RESP3                     | ✅          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |
| Snowflake SDK (HTTP-based)      | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ✅          |
| Snowflake REST API (HTTP-based) | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ✅          |
| Amazon Redshift 3.0             | ✅          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |
| Oracle Database                 | ✅\*        | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ✅                     | ❌          |

> \* *Oracle Database on Kubernetes requires [transparent steering](../user-guide/deploy-install/advanced-options/agent-proxy/selective-transparent-steering.md) configured for the Oracle database host.*

## Client Workload Identifiers

[Section titled “Client Workload Identifiers”](#client-workload-identifiers)

| Client Workload Identifiers | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Virtual Appliance | Docker-compose on VMs | AWS Lambda |
| --------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | ----------------- | --------------------- | ---------- |
| Aembit Client ID            | ✅          | ✅               | ✅               | ✅                       | ✅                         | ❌                 | ✅                     | ✅          |
| AWS Account ID              | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ❌          |
| AWS EC2 Instance ID         | ❌          | ⚪️              | ⚪️              | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ❌          |
| AWS ECS Task Family         | ⚪️         | ⚪️              | ✅               | ⚪️                      | ⚪️                        | ⚪️                | ⚪️                    | ⚪️         |
| AWS Region                  | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ❌          |
| AWS Subscription ID         | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ❌          |
| AWS VM ID                   | ❌          | ❌               | ❌               | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ⚪️         |
| Hostname                    | ❌          | ❌               | ❌               | ✅                       | ✅                         | ❌                 | ✅                     | ❌          |
| Kubernetes Pod name         | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                | ⚪️                    | ⚪️         |
| Kubernetes Pod name prefix  | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                | ⚪️                    | ⚪️         |
| Process Command Line \*\*   | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ❌                     | ❌          |
| Process Name \*\*           | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ❌                     | ❌          |
| Process Path \*\*           | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ❌                     | ❌          |
| Process User Name \*\*      | ❌          | ❌               | ❌               | ✅                       | ❌                         | ❌                 | ❌                     | ❌          |
| Source IP                   | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ❌          |
| AWS Lambda ARN              | ⚪️         | ⚪️              | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                | ⚪️                    | ✅          |

> \* *These Client Workload identifiers are available for their respective cloud platforms only*.\
> \*\* *Before using process-based identifiers, you must enable them in Agent Proxy first.* *See [Process Command Line](../user-guide/access-policies/client-workloads/identification/process-command-line.md), [Process Name](../user-guide/access-policies/client-workloads/identification/process-name.md), [Process Path](../user-guide/access-policies/client-workloads/identification/process-path.md), and [Process User Name](../user-guide/access-policies/client-workloads/identification/process-user-name.md) for details*

## Agent Controller Trust Providers

[Section titled “Agent Controller Trust Providers”](#agent-controller-trust-providers)

| Trust Providers                 | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine | Virtual Appliance | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | --------------- | ----------------- | --------------------- | ---------- |
| AWS Role                        | ❌          | ❌               | ✅               | ❌               | ❌                 | ⚪️                    | ⚪️         |
| AWS Metadata Service            | ✅\*        | ❌               | ❌               | ✅\*             | ❌                 | ⚪️                    | ⚪️         |
| Azure Instance Metadata Service | ✅\*        | ⚪️              | ⚪️              | ✅\*             | ❌                 | ⚪️                    | ⚪️         |
| GCP Identity Token              | ✅\*        | ⚪️              | ⚪️              | ✅\*             | ❌                 | ⚪️                    | ⚪️         |
| Kubernetes Service Account      | ✅          | ✅               | ⚪️              | ⚪️              | ⚪️                | ⚪️                    | ⚪️         |
| Kerberos                        | ⚪️         | ⚪️              | ⚪️              | ⚪️              | ⚪️                | ⚪️                    | ⚪️         |

> \* *Aembit tailors the Trust Providers available in Kubernetes and VM environments specifically for their respective cloud platforms*.

## Agent Proxy Trust Providers

[Section titled “Agent Proxy Trust Providers”](#agent-proxy-trust-providers)

| Trust Providers                 | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Virtual Appliance | Docker-compose on VMs | AWS Lambda |
| ------------------------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | ----------------- | --------------------- | ---------- |
| AWS Role                        | ❌          | ❌               | ✅               | ✅\*\*                   | ✅\*\*                     | ❌                 | ❌                     | ✅          |
| AWS Metadata Service            | ✅\*        | ❌               | ❌               | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ❌          |
| Azure Instance Metadata Service | ✅\*        | ⚪️              | ⚪️              | ✅\*                     | ✅\*                       | ❌                 | ✅\*                   | ⚪️         |
| GCP Identity Token              | ⚪️         | ⚪️              | ⚪️              | ❌                       | ❌                         | ❌                 | ❌                     | ⚪️         |
| Kubernetes Service Account      | ✅          | ✅               | ⚪️              | ⚪️                      | ⚪️                        | ⚪️                | ⚪️                    | ⚪️         |
| Kerberos                        | ❌          | ❌               | ❌               | ✅                       | ✅                         | ❌                 | ✅                     | ⚪️         |

> \* *Aembit tailors the Trust Providers available in Kubernetes and VM environments specifically for their respective cloud platforms*.\
> \*\* *The AWS Role Trust Provider supports only EC2 instances with an attached IAM role*.

## Conditional Access

[Section titled “Conditional Access”](#conditional-access)

| Access Conditions | Kubernetes | AWS EKS Fargate | AWS ECS Fargate | Virtual Machine (Linux) | Virtual Machine (Windows) | Virtual Appliance | Docker-compose on VMs | AWS Lambda |
| ----------------- | ---------- | --------------- | --------------- | ----------------------- | ------------------------- | ----------------- | --------------------- | ---------- |
| CrowdStrike       | ❌          | ❌               | ❌               | ✅                       | ✅                         | ❌                 | ✅                     | ❌          |
| Wiz               | ✅          | ✅               | ❌               | ❌                       | ❌                         | ❌                 | ❌                     | ✅          |
| Time              | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ✅          |
| GeoIP             | ✅          | ✅               | ✅               | ✅                       | ✅                         | ✅                 | ✅                     | ✅          |

## Supported operating systems for VMs

[Section titled “Supported operating systems for VMs”](#supported-operating-systems-for-vms)

The following sections contain the operating system versions that Aembit Agent Proxy and Agent controller supports on VMs

### Linux distributions

[Section titled “Linux distributions”](#linux-distributions)

| Linux Distribution | Version |
| ------------------ | ------- |
| Ubuntu             | 20.04   |
| Ubuntu             | 22.04   |
| Red Hat            | 8.6     |
| Red Hat            | 8.9     |
| Red Hat            | 9.3     |

### Windows editions

[Section titled “Windows editions”](#windows-editions)

| Windows Edition | Version |
| --------------- | ------- |
| Windows Server  | 2019    |
| Windows Server  | 2022    |

## CLI support

[Section titled “CLI support”](#cli-support)

### CLI operating system support

[Section titled “CLI operating system support”](#cli-operating-system-support)

You can use the Aembit CLI with the following operating system versions:

#### Linux distributions

[Section titled “Linux distributions”](#linux-distributions-1)

| Linux Distribution | Version |
| ------------------ | ------- |
| Ubuntu             | 22.04   |
| Red Hat            | 9.3     |

#### Windows editions

[Section titled “Windows editions”](#windows-editions-1)

| Windows Edition | Version |
| --------------- | ------- |
| Windows         | 10      |
| Windows Server  | 2019    |
| Windows Server  | 2022    |

### CLI CI/CD runner support

[Section titled “CLI CI/CD runner support”](#cli-cicd-runner-support)

The Aembit CLI is compatible with the following CI/CD runners:

#### GitHub-hosted runners

[Section titled “GitHub-hosted runners”](#github-hosted-runners)

For more information, see [GitHub runners documentation](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners).

| GitHub Runner    |
| ---------------- |
| `ubuntu-latest`  |
| `windows-latest` |

#### GitLab-hosted runners

[Section titled “GitLab-hosted runners”](#gitlab-hosted-runners)

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

[Section titled “CLI deployment model support”](#cli-deployment-model-support)

The Aembit CLI supports the following deployment models:

* [GitHub Actions](../user-guide/deploy-install/ci-cd/github/github-edge-cli.md)
* [GitLab Jobs](../user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-cli.md)
* [Jenkins Pipelines](../user-guide/deploy-install/ci-cd/jenkins-pipelines.md)
* Environments that provide OIDC tokens. See [OIDC ID Token Trust Provider](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) for more info.

### CLI Client Workload Identifiers

[Section titled “CLI Client Workload Identifiers”](#cli-client-workload-identifiers)

The Aembit CLI supports the following Client Workload Identifiers:

* [Aembit Client ID](../user-guide/access-policies/client-workloads/identification/aembit-client-id.md)

### CLI Trust Providers

[Section titled “CLI Trust Providers”](#cli-trust-providers)

The Aembit CLI supports the following Trust Providers:

* [AWS Metadata Service Trust Provider](../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role Trust Provider](../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)
* [GitHub Trust Provider](../user-guide/access-policies/trust-providers/github-trust-provider.md)
* [GitLab Trust Provider](../user-guide/access-policies/trust-providers/gitlab-trust-provider.md)
* [Kubernetes Service Account Trust Provider](../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)
* [OIDC ID Token Trust Provider](../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md)

### CLI Access Conditions

[Section titled “CLI Access Conditions”](#cli-access-conditions)

The Aembit CLI supports the following Access Conditions:

* [GeoIP](../user-guide/access-policies/access-conditions/aembit-geoip.md)
* [Time](../user-guide/access-policies/access-conditions/aembit-time-condition.md)
* [CrowdStrike](../user-guide/access-policies/access-conditions/crowdstrike.md)
