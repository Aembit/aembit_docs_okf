---
type: reference
title: "Credential Providers"
description: "This document provides a high-level description of Credential Providers"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Credential Providers

This section covers Credential Providers in Aembit, which you can use to provide access credentials to Client Workloads so they can access Server Workloads securely.

> **Reuse this in another Resource Set**
>
> You can copy a Credential Provider to another Resource Set. See [About component copying](../../administration/resource-sets/about-component-copying.md) to learn more.

The following pages provide information about different Credential Provider types and how to configure them:

* [Aembit Access Token](aembit-access-token.md)
* [API Key](api-key.md)
* [AWS Secrets Manager](aws-secrets-manager.md)
* [AWS Security Token Service Federation](aws-security-token-service-federation.md)
* [AWS SigV4](aws-sigv4.md)
* [Azure Entra Workload Identity Federation](azure-entra-workload-identity-federation.md)
* [Azure Key Vault](azure-key-vault.md)
* [Claude Workload Identity Federation](claude-workload-identity-federation.md)
* [Google GCP Workload Identity Federation](google-workload-identity-federation.md)
* [JSON Web Token (JWT)](json-web-token.md)
* [JWT-SVID Token](spiffe-jwt-svid.md)
* [Managed GitLab Account](managed-gitlab-account.md)
* [MCP User-Based Access Token](mcp-user-based-access-token.md)
* [OAuth 2.0 Authorization Code](oauth-authorization-code.md)
* [OAuth 2.0 Client Credentials](oauth-client-credentials.md)
* [OIDC ID Token](oidc-id-token.md)
* [OpenAI API Workload Identity Federation](openai-workload-identity-federation.md)
* [Username Password](username-password.md)
* [Vault Client Token](vault-client-token.md)
* [X.509-SVID](spiffe-x509-svid.md)

### About Credential Providers

* [About JWT-SVID Tokens](about-spiffe-jwt-svid.md)
* [About MCP User-Based Access Tokens](about-mcp-user-based-access-token.md)
* [About OIDC ID Tokens](about-oidc-id-token.md)
* [About X.509-SVID](about-spiffe-x509-svid.md)

### Advanced options

* [Private Network Access](private-network-access.md)
* [Multiple Credential Providers](multiple-credential-providers.md)
* [HashiCorp Vault Dynamic Claims](advanced-options/dynamic-claims-vault.md)
* [OIDC ID Token Dynamic Claims](advanced-options/dynamic-claims-oidc.md)
* [Multiple Credential Providers Terraform](advanced-options/multiple-credential-providers-terraform.md)

### Integrations

* [About Credential Provider Integrations](integrations/overview.md)
* [AWS IAM Role](integrations/aws-iam-role.md)
* [Azure Entra Federation](integrations/azure-entra-federation.md)
* [GitLab Dedicated Self-Managed](integrations/gitlab-dedicated-self.md)
* [GitLab Service Account](integrations/gitlab.md)
