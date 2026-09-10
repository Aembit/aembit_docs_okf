---
type: reference
title: "Credential Provider integrations overview"
description: "An overview of what Credential Provider integrations are and how they work"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/integrations/
interface: web-ui
tags: ["integration", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Credential Provider integrations overview

Aembit Credential Provider Integrations associate a third-party system (such as GitLab) with your Credential Providers to perform credential lifecycle management on your behalf. Credential Providers that use Credential Provider Integrations are responsible for maintaining an always-available credential value, which Aembit injects as part of an Access Policy.

Aembit’s credential lifecycle management capabilities include creating, rotating, and deleting tokens.

## Configure Credential Provider Integrations

![AWS Icon](https://docs.aembit.io/3p-logos/aws-icon.svg)

[AWS IAM Role](aws-iam-role.md)Integrate with AWS IAM Roles Anywhere for credential management.

→

![Azure Icon](https://docs.aembit.io/3p-logos/azure-icon2.svg)

[Azure Entra Federation](azure-entra-federation.md)Integrate with Azure Key Vault using Workload Identity Federation.

→

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab.com](gitlab.md)Integrate with GitLab.com for service account management.

→

![GitLab Icon](https://docs.aembit.io/3p-logos/gitlab-icon.svg)

[GitLab Dedicated/Self-Managed](gitlab-dedicated-self.md)Integrate with GitLab Dedicated or Self-Managed instances.

→

## How Credential Provider Integrations work

In general, Credential Provider Integrations use the following process:

1. When you initially create Credential Provider, Aembit creates the third-party account or credential or both and securely stores it in Aembit’s database.

2. Once 80% of the configured Credential Provider’s **Lifetime** expires, Aembit rotates the third-party credential and securely stores the updated credential in Aembit’s database.

3. When properly requested and authorized, Aembit provides the third-party credential from Aembit’s database to the associated Agent Proxy.

   If the injected credential fails, Agent Proxy continues to log the existing Workload Events to indicate the failure but doesn’t generate a notification or take explicit action. For example, if you delete a credential on your third-party system, then the Workload fails until Aembit successfully rotates the credential.

4. When you delete a Credential Provider, Aembit deletes the third-party account and credential.

   > **Deleting integrations**
   >
   > You can’t delete a Credential Provider Integration until you delete all its associated Credential Providers.
   >
   > You can’t change the association between a Credential Provider Integration and a Credential Provider after you create it.

### Azure Entra Federation integration

The [Azure Entra Federation](azure-entra-federation.md) integration enables Aembit to securely access Microsoft Azure resources—such as Azure Key Vault—on behalf of your workloads, without requiring long-lived secrets or static credentials. It leverages Azure’s Workload Identity Federation, allowing Aembit to authenticate using short-lived, federated tokens based on OpenID Connect (OIDC) standards.

#### Process flow

At a high level, the Azure Entra Federation Credential Provider Integration works like this:

1. You register an application in Azure Entra ID (formerly Azure Active Directory) and configure a federated credential that trusts tokens issued by Aembit.

2. In Aembit, you create an Azure Entra Federation integration, providing details from your Azure application and the OIDC issuer information from Aembit.

3. When a workload requests access to an Azure resource, Aembit generates an OIDC token and presents it to Azure.

4. Azure validates the token and issues a short-lived Azure access token scoped for the requested resource.

5. Aembit uses this token to access Azure resources (like Key Vault) and delivers the result securely to the requesting workload, governed by Aembit’s access policies.

### GitLab Service Account integration

This [GitLab Service Account](gitlab.md) integration uses your GitLab administrator account to connect with your GitLab instance and control credential lifecycle management for each Managed GitLab Account Credential Provider.

When creating a [Managed GitLab Account Credential Provider](../managed-gitlab-account.md), you scope it to only access specific GitLab Projects or GitLab Groups. Each provider creates an additional, separate GitLab service account that manages credentials on your behalf. This approach gives you fine-grained control over your GitLab workloads’ credential lifecycle management.

#### GitLab subscriptions

Depending on the type of [GitLab plan](https://docs.gitlab.com/subscriptions/choosing_subscription/) you have, you have different choices of how to set up your GitLab Service Account integration.

* For [GitLab.com plans](gitlab.md), you must use `https://gitlab.com` when creating the integration.
* For [GitLab Dedicated or Self-Managed plans](gitlab-dedicated-self.md), you must use the URL of your GitLab dedicated or Self-Managed instance’s.

See [GitLab’s plans](https://docs.gitlab.com/subscriptions/choosing_subscription/) for details about GitLab subscription types.

> **GitLab plan differences**
>
> The distinction between the different GitLab plans requires you to use different API calls when creating the GitLab Service Account integration.

#### Process flow

At a high level, the GitLab Service Account Credential Provider Integration works like this:

1. You initially connect Aembit to GitLab using your GitLab administrator account.

2. You create a Credential Provider with Managed GitLab Account integration.

3. Aembit creates a service account for each Credential Provider with your specified access scope.

4. Aembit securely stores credentials in its database.

5. Aembit automatically rotates credentials before expiration.

6. When requested and authorized, Aembit provides credentials to the Agent Proxy.
