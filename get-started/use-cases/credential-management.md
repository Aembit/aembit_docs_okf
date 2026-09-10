---
type: explanation
title: "Securing credential management"
description: "How Aembit enables you to centrally manage and control credentials in your environments"
resource: https://docs.aembit.io/get-started/use-cases/credential-management/
tags: ["use-case"]
timestamp: 2026-04-13T13:05:24-07:00
---

# Securing credential management

Teams running workloads in Kubernetes, VMs, or serverless environments need to distribute and rotate credentials. Manual rotation is error-prone and often gets skipped. Credentials end up in environment variables, configuration maps, or even checked into source control. When someone leaves the company or a secret leaks, rotating everything becomes an urgent task.

Why Kubernetes Secrets aren’t secure by default

Kubernetes Secrets are base64-encoded, not encrypted. Anyone with `kubectl` access and the right Role-Based Access Control (RBAC) permissions can decode them. The secrets sit in `etcd`, Kubernetes’ backing datastore, where they persist unencrypted unless you explicitly configure encryption at rest.

Even with encryption at rest enabled, any pod that mounts the secret and any user with RBAC read permissions can access the plaintext value. Kubernetes Secrets solve distribution (getting the secret to the pod) but not access control (restricting which workloads should have it) or lifecycle management (rotating the secret when it changes).

Aembit addresses all three: workloads receive credentials only after identity verification, Access Policies scope credentials to specific applications, and rotation happens transparently through the vault.

## What Aembit solves

Aembit centralizes credential management and ties access to [workload identity](../concepts/client-workloads.md). Aembit delivers credentials just-in-time to requesting workloads, scopes them to specific applications, and rotates them automatically:

* [Credential Providers](../../user-guide/access-policies/credential-providers/overview.md) integrate with AWS Secrets Manager, Azure Key Vault, HashiCorp Vault, and other secret storage systems
* Workloads authenticate to Aembit using their identity (Kubernetes ServiceAccount, VM metadata, OIDC token) via [Trust Providers](../concepts/trust-providers.md)
* Aembit retrieves the secret from your vault and injects it into the workload at runtime—no secrets stored in configuration files
* Rotating a secret in the vault is seamless because Aembit always delivers the latest version to requesting workloads

## Real example: Using cloud credential vaults

Imagine you’re running Kubernetes workloads across multiple environments that all need database passwords. You’re storing these passwords in Kubernetes Secrets or ConfigMaps, which means they’re stored in `etcd` (Kubernetes’ database) and visible to anyone with cluster access. Rotating passwords is a manual process: update the secret, restart the deployment, hope nothing breaks.

Aembit changes this model entirely:

![Kubernetes workload authenticating through Aembit to retrieve secrets from AWS Secrets Manager and inject them at runtime](https://docs.aembit.io/d2/docs/get-started/use-cases/credential-management-0.svg)

Your Kubernetes workloads authenticate to Aembit using their [Kubernetes ServiceAccount](../../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md). Aembit verifies this identity and checks whether your [Access Policies](../concepts/access-policies.md) permit the workload to request database credentials.

If approved, Aembit connects to your AWS Secrets Manager or Azure Key Vault to retrieve the current database password. You can see all available [credential providers](../../user-guide/access-policies/credential-providers/overview.md) for different vault systems.

Instead of storing the password in Kubernetes, Aembit injects it directly into the workload’s memory at runtime. The workload uses it to connect to the database, then Aembit automatically cleans it up.

When you rotate the password in your vault, you don’t need to update any Kubernetes manifests or restart deployments. The next time a workload requests credentials, it automatically gets the new password from your vault. You can [audit every credential request](../concepts/audit-report.md) for compliance, knowing exactly which workload accessed what secret and when.

### Why this matters for credential management

Secrets stored in Kubernetes Secrets or ConfigMaps sit in `etcd` and are visible to anyone with cluster access. Rotating them means updating manifests, restarting pods, and coordinating across teams. One missed update breaks a deployment.

Aembit removes secrets from your infrastructure entirely. Workloads authenticate with their existing identity, Aembit retrieves the current credential from your vault, and the workload never stores it. Rotation happens in the vault and takes effect on the next request with no restarts or manifest changes.

## Supported platforms

* AWS Secrets Manager
* Azure Key Vault
* HashiCorp Vault
* Snowflake (keypair rotation)

## Next steps

* [Configure a Credential Provider](../../user-guide/access-policies/credential-providers/overview.md) for your vault system
* [Set up Trust Providers](../concepts/trust-providers.md) to authenticate your workloads by identity
* [Define Access Policies](../concepts/access-policies.md) to control which workloads can request which credentials

**Related use cases:**

* For securing database connections specifically, see [Database Access](database-access.md)
* For credentials needed by CI/CD pipelines, see [CI/CD Pipelines](ci-cd.md)
* For managing credentials across cloud providers, see [Multicloud Environments](multicloud.md)
