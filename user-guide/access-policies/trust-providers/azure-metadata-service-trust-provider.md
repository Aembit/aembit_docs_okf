---
type: reference
title: "Azure Instance Metadata Service trust provider"
description: "This page describes the steps required to configure the Azure Instance Metadata Service Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Azure Instance Metadata Service trust provider


The Azure Instance Metadata Service Trust Provider supports attestation of Client Workloads and Agent Controller identities in an [Azure](https://azure.microsoft.com/) environment.

The Azure Instance Metadata Service Trust Provider relies on the [Azure Instance Metadata Service (IMDS)](https://learn.microsoft.com/en-us/azure/virtual-machines/instance-metadata-service?tabs=linux) to retrieve an instance identity document.

## Match rules

The following match rules are available for this Trust Provider type:

* sku
* subscriptionId
* vmId

Please refer to the [Azure documentation](https://learn.microsoft.com/en-us/azure/virtual-machines/instance-metadata-service?tabs=linux#attested-data) for a detailed description of match rule fields available in the identity document.

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Aembit Client ID](../client-workloads/identification/aembit-client-id.md)
* [Azure Subscription ID](../client-workloads/identification/azure-subscription-id.md)
* [Azure VM ID](../client-workloads/identification/azure-vm-id.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
