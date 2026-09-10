---
type: reference
title: "Advanced Credential Provider Options"
description: "Overview of advanced configuration options for Aembit Credential Providers"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/advanced-options/
interface: web-ui
tags: ["advanced-option", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Advanced Credential Provider Options

This section covers advanced configuration options and features for Aembit Credential Providers. These features provide additional flexibility and functionality for specific use cases and environments.

## Dynamic claims

Dynamic claims allow you to create personalized and context-aware credentials by extracting values from tokens or environment variables at runtime.

### OIDC ID Token dynamic claims

Configure dynamic claims for [OIDC ID Token Credential Providers](../oidc-id-token.md) to extract and use values from incoming OIDC tokens.

* Extract claims from OIDC token payloads using `${oidc.identityToken.decode.payload.claim_name}` syntax
* Access environment variables with `${os.environment.VARIABLE_NAME}`
* Combine values to create custom claim formats

[Learn more about OIDC Dynamic Claims](dynamic-claims-oidc.md)

### Vault dynamic claims

Configure dynamic claims for [Vault Client Token Credential Providers](../vault-client-token.md) to create workload-specific credentials.

* Collect information from Kubernetes ConfigMaps and environment variables
* Support for Agent Proxy version 1.9.142 and later
* Enable workloads to specify claim values outside the Aembit Tenant UI

[Learn more about Vault Dynamic Claims](dynamic-claims-vault.md)

## Multiple Credential Providers

Learn how to configure and manage multiple Credential Providers in Access Policies using the Aembit Cloud UI.

[Configure Multiple Credential Providers](../multiple-credential-providers.md)

### Multiple Credential Providers with Terraform

Automate the configuration of multiple Credential Providers using Terraform for infrastructure-as-code deployments.

[Configure with Terraform](multiple-credential-providers-terraform.md)

## Related docs

* [Credential Providers Overview](../overview.md)
* [OIDC ID Token Credential Provider](../oidc-id-token.md)
* [Vault Client Token Credential Provider](../vault-client-token.md)
