---
type: how-to
title: "Private Network Access for Credential Providers"
description: "How to use Private Network Access to retrieve credentials from secrets managers in private networks"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/private-network-access/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-01-16T16:50:50-08:00
type_inferred: true
---

# Private Network Access for Credential Providers

Private Network Access (PNA) allows Aembit to retrieve credentials from secrets managers in your private network. This includes secrets managers accessible only within an AWS Virtual Private Cloud (VPC) or Azure Virtual Network.

By default, Aembit Cloud connects directly to external secrets managers (like AWS Secrets Manager, Azure Key Vault, or HashiCorp Vault) to retrieve credentials on behalf of your workloads. However, if you restrict your secrets manager to a private network, Aembit Cloud can’t reach it.

With PNA, credential retrieval happens through your Aembit Edge component (Aembit CLI or Agent Proxy) instead of Aembit Cloud. This allows you to keep your secrets manager in a private network while still using Aembit for workload identity and access management.

## When to use Private Network Access

[Section titled “When to use Private Network Access”](#when-to-use-private-network-access)

Enable PNA when:

* Your secrets manager endpoint is only reachable from within a private network or VPC endpoint
* You don’t want to maintain IP allowlists for Aembit Cloud in your cloud environment
* You want all access to your secrets manager to originate from your own infrastructure

## How it works

[Section titled “How it works”](#how-it-works)

When you enable PNA for a Credential Provider:

1. **Aembit Cloud instructs your Edge component** to retrieve the credential using the integration you configured.
2. **The Edge component accesses the secrets manager** from your private network and reads the secret.
3. **Aembit receives the secret value** and injects it into your Server Workloads according to your Access Policies.

Enabling PNA only affects *where* Aembit retrieves credentials from (Aembit Cloud vs your Edge component). It doesn’t change *how* Aembit delivers credentials to your Server Workloads—your Access Policies and Server Workload configuration still control those behaviors.

## Requirements

[Section titled “Requirements”](#requirements)

PNA requires:

* An Aembit Edge component (Aembit CLI or Agent Proxy) running in your private network
* Network connectivity from the Edge component to your secrets manager
* The same integration and permissions you would use without PNA

### Agent Proxy version requirements

[Section titled “Agent Proxy version requirements”](#agent-proxy-version-requirements)

| Credential Provider          | Minimum Version  | Recommended Version    | Notes                                                                                                                                                                                                                |
| ---------------------------- | ---------------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HashiCorp Vault Client Token | Agent Proxy 1.20 | Agent Proxy 1.20+      | Initial and current PNA behavior are the same. When you enable PNA, all Vault access for this provider runs through your Edge component.                                                                             |
| AWS Secrets Manager Value    | Agent Proxy 1.25 | Agent Proxy 1.28.4063+ | Agent Proxy 1.25 adds basic PNA support so your Edge component can retrieve secrets. Use Agent Proxy 1.28.4063+ for full PNA support, where your Edge component handles all AWS access for this Credential Provider. |
| Azure Key Vault Value        | Agent Proxy 1.26 | Agent Proxy 1.26+      | Private Network Access for Azure Key Vault requires Agent Proxy 1.26 or later. When you enable PNA, your Edge component handles all Key Vault access for this provider.                                              |

## Supported Credential Providers

[Section titled “Supported Credential Providers”](#supported-credential-providers)

The following Credential Providers support PNA:

| Credential Provider                                                                                  | PNA Support | Limitations                                          |
| ---------------------------------------------------------------------------------------------------- | ----------- | ---------------------------------------------------- |
| [HashiCorp Vault Client Token](vault-client-token.md) | Supported   | None                                                 |
| [AWS Secrets Manager Value](aws-secrets-manager.md)   | Supported   | HTTP Basic Auth with Username/Password not supported |
| [Azure Key Vault Value](azure-key-vault.md)           | Supported   | None                                                 |

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

If credential retrieval fails with PNA enabled:

* **Check network connectivity:** Confirm the host running the Aembit CLI or Agent Proxy can reach your secrets manager endpoint (check DNS resolution, firewall rules, and VPC peering/endpoints)
* **Verify permissions:** Confirm the integration’s identity (IAM role, service principal, or Vault token) has permission to read the specified secret
* **Check secret format:** Ensure the secret data format matches your selected Credential Value Type

For provider-specific troubleshooting, see the individual Credential Provider documentation in the preceding section.

## Related topics

[Section titled “Related topics”](#related-topics)

* [AWS Secrets Manager Credential Provider](aws-secrets-manager.md)
* [Azure Key Vault Credential Provider](azure-key-vault.md)
* [HashiCorp Vault Client Token Credential Provider](vault-client-token.md)
