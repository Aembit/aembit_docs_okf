---
type: reference
title: "Client Workload Identifiers overview"
description: "This page provides a high-level description of Client Workload Identifiers in Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/
interface: web-ui
tags: ["identification", "client-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Client Workload Identifiers overview

Client Workload identification is an initial step to recognize the specific software application, script, or automated process that initiates an access request to a Server Workload. This identification is critical because it’s a prerequisite for matching the request to the correct Access Policy and invoking the appropriate Trust Provider for identity attestation. Accurate identification is essential for enforcing the principle of least privilege and preventing misidentification which could lead to security vulnerabilities.

Aembit addresses the need for accurate identification across diverse and heterogeneous environments by offering a variety of methods tailored to different deployment contexts. These methods leverage native identity constructs and environmental evidence available in those platforms.

Examples of Aembit Client Workload identification methods include:

* **Kubernetes** - Using the Pod Name Prefix, the exact Pod Name, or the Kubernetes Service Account under which the container runs.
* **Cloud Platforms (AWS, Azure)** - Using Instance Metadata Attributes (like instance ID or tags), AWS IAM Role ARN, Azure Subscription ID, or Azure VM ID.
* **CI/CD Systems (GitHub Actions, GitLab Jobs)** - Inspecting claims within ephemeral OpenID Connect (OIDC) tokens, such as repository name, subject, namespace path, or project path.
* **Serverless Platforms (AWS Lambda)** - Using the unique AWS Lambda Function ARN.
* **Virtual Machines (VMs)** - Identifying by Hostname, Process Name, or both.
* **MCP Clients** - Using the redirect URI from Dynamic Client Registration, or the URL of a Client ID Metadata Document (CIMD), to identify MCP clients connecting through the MCP Authorization Server.
* **Aembit Native** - A unique Aembit Client ID that Aembit assigns for scenarios where other identifiers won’t work.

Aembit supports [configuring multiple identifiers](client-workload-multiple-ids.md) for a single Client Workload definition, to increase its uniqueness when identifying your Client Workloads.

## Available Client Workload identification methods

Aembit supports a variety of identification methods for Client Workloads, allowing you to choose the most suitable one based on your deployment environment and requirements. Each method provides a unique way to identify workloads, making sure that Aembit applies your Policies accurately.

These methods include identifiers based on cloud provider resources, Kubernetes configurations, and more. The choice of identifier can depend on the specific characteristics of your workloads and the environments in which they operate.

The following sections are the different identification methods available:

### Generic Client Workload Identifiers

![Aembit Icon](https://docs.aembit.io/aembit-icons/aembit-icon-color.svg)

[Aembit Client ID](aembit-client-id.md)Identify workloads by their Aembit Client ID.

→

![Computer Icon](https://docs.aembit.io/aembit-icons/client-workload.svg)

[Hostname](hostname.md)Identify workloads by their hostname.

→

![Gear With Code Icon](https://docs.aembit.io/aembit-icons/gear-complex-code-light.svg)

[Process Name](process-name.md)Identify workloads by their process name.

→

![Gear With Code Icon](https://docs.aembit.io/aembit-icons/gear-complex-code-light.svg)

[Process Command Line](process-command-line.md)Identify workloads by their process command line.

→

![Gear With Code Icon](https://docs.aembit.io/aembit-icons/gear-complex-code-light.svg)

[Process Path](process-path.md)Identify workloads by their executable path.

→

![Gear With Code Icon](https://docs.aembit.io/aembit-icons/gear-complex-code-light.svg)

[Process User Name](process-user-name.md)Identify workloads by their process user name.

→

![Computer Icon](https://docs.aembit.io/aembit-icons/client-workload.svg)

[Source IP Address](source-ip.md)Identify workloads by their source IP address.

→

### AWS Client Workload Identifiers

![AWS Icon](https://docs.aembit.io/3p-logos/aws-icon.svg)

[AWS Account ID](aws-account-id.md)Identify workloads by their AWS Account ID.

→

![AWS EC2 Icon](https://docs.aembit.io/3p-logos/aws-ec2-icon.svg)

[AWS EC2 Instance ID](aws-ec2-instance-id.md)Identify workloads by their AWS EC2 Instance ID.

→

![AWS ECS Icon](https://docs.aembit.io/3p-logos/aws-ecs-icon.svg)

[AWS ECS Task Family](aws-ecs-task-family.md)Identify workloads by their AWS ECS Task Family.

→

![AWS ECS Icon](https://docs.aembit.io/3p-logos/aws-ecs-icon.svg)

[AWS ECS Service Name](aws-ecs-service-name.md)Identify workloads by their AWS ECS Service Name.

→

![AWS Lambda Icon](https://docs.aembit.io/3p-logos/aws-lambda-icon.svg)

[AWS Lambda ARN](aws-lambda-arn.md)Identify workloads by their AWS Lambda ARN.

→

![AWS Region Icon](https://docs.aembit.io/3p-logos/aws-icon.svg)

[AWS Region](aws-region.md)Identify workloads by their AWS Region.

→

### Azure Client Workload Identifiers

![Azure Icon](https://docs.aembit.io/3p-logos/azure-icon2.svg)

[Azure Subscription ID](azure-subscription-id.md)Identify workloads by their Azure Subscription ID.

→

![Azure Icon](https://docs.aembit.io/3p-logos/azure-icon2.svg)

[Azure VM ID](azure-vm-id.md)Identify workloads by their Azure VM ID.

→

### GCP Client Workload Identifiers

![GCP Icon](https://docs.aembit.io/3p-logos/gcp-icon.svg)

[GCP Identity Token](gcp-identity-token.md)Identify workloads by their GCP Identity Token email.

→

### GitHub Client Workload Identifiers

![GitHub Icon](https://docs.aembit.io/3p-logos/github-icon.svg)

[GitHub ID Token Repository](github-id-token-repository.md)Identify workloads by their GitHub ID Token Repository.

→

![GitHub Icon](https://docs.aembit.io/3p-logos/github-icon.svg)

[GitHub ID Token Subject](github-id-token-subject.md)Identify workloads by their GitHub ID Token Subject.

→

### GitLab Client Workload Identifiers

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab ID Token Namespace Path](gitlab-id-token-namespace-path.md)Identify workloads by their GitLab ID Token Namespace Path.

→

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab ID Token Project Path](gitlab-id-token-project-path.md)Identify workloads by their GitLab ID Token Project Path.

→

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab ID Token Ref Path](gitlab-id-token-ref-path.md)Identify workloads by their GitLab ID Token Ref Path.

→

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab ID Token Subject](gitlab-id-token-subject.md)Identify workloads by their GitLab ID Token Subject.

→

### Kubernetes Client Workload Identifiers

![Kubernetes Icon](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes Namespace](kubernetes-namespace.md)Identify workloads by their Kubernetes Namespace.

→

![Kubernetes Icon](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes Pod Name Prefix](kubernetes-pod-name-prefix.md)Identify workloads by their Kubernetes Pod Name Prefix.

→

![Kubernetes Icon](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes Pod Name](kubernetes-pod-name.md)Identify workloads by their Kubernetes Pod Name.

→

![Kubernetes Icon](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes Service Account Name](kubernetes-service-account-name.md)Identify workloads by their Kubernetes Service Account Name.

→

![Kubernetes Icon](https://docs.aembit.io/3p-logos/kubernetes-icon.svg)

[Kubernetes Service Account UID](kubernetes-service-account-name.md)Identify workloads by their Kubernetes Service Account UID.

→

### OIDC Client Workload Identifiers

![JWT Icon](https://docs.aembit.io/3p-logos/jwt-icon.svg)

[OIDC ID Token](oidc-id-token.md)Identify workloads by a custom claim in an OIDC ID token.

→

![JWT Icon](https://docs.aembit.io/3p-logos/jwt-icon.svg)

[OIDC ID Token Audience](oidc-id-token-audience.md)Identify workloads by the audience claim in an OIDC ID token.

→

![JWT Icon](https://docs.aembit.io/3p-logos/jwt-icon.svg)

[OIDC ID Token Issuer](oidc-id-token-issuer.md)Identify workloads by the issuer claim in an OIDC ID token.

→

![JWT Icon](https://docs.aembit.io/3p-logos/jwt-icon.svg)

[OIDC ID Token Subject](oidc-id-token-subject.md)Identify workloads by the subject claim in an OIDC ID token.

→

### Terraform Cloud

![Terraform Icon](https://docs.aembit.io/3p-logos/terraform-icon.svg)

[Terraform Cloud ID Token Organization ID](terraform-cloud-id-token-organization-id.md)Identify workloads by Terraform Cloud ID Token Organization ID.

→

![Terraform Icon](https://docs.aembit.io/3p-logos/terraform-icon.svg)

[Terraform Cloud ID Token Project ID](terraform-cloud-id-token-project-id.md)Identify workloads by Terraform Cloud ID Token Project ID.

→

![Terraform Icon](https://docs.aembit.io/3p-logos/terraform-icon.svg)

[Terraform Cloud ID Token Workspace ID](terraform-cloud-id-token-workspace-id.md)Identify workloads by Terraform Cloud ID Token Workspace ID.

→

### MCP Client Workload Identifiers

![Client Workload Icon](https://docs.aembit.io/aembit-icons/client-workload.svg)

[Redirect URI](redirect-uri.md)Identify MCP clients by their OAuth redirect URI.

→

![Client Workload Icon](https://docs.aembit.io/aembit-icons/client-workload.svg)

[CIMD Client ID](cimd-client-id.md)Identify MCP clients by the URL of their Client ID Metadata Document.

→
