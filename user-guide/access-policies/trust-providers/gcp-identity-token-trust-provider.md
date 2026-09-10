---
type: reference
title: "GCP Identity Token Trust Provider"
description: "This page describes the steps required to configure the GCP Identity Token Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/gcp-identity-token-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# GCP Identity Token Trust Provider

The GCP Identity Token Trust Provider verifies the identities of workloads running within Google Cloud Platform (GCP) by validating identity tokens issued by GCP. These tokens carry metadata, such as the email associated with the service account or user executing the operation, ensuring secure and authenticated access to GCP resources.

To validate the user identity that Google Cloud Identity-Aware Proxy (IAP) asserts for workloads behind IAP, see [GCP IAP JWT Trust Provider](gcp-iap-jwt-trust-provider.md).

## Match rules

The following match rule is available for this Trust Provider type:

| Data  | Description                                               | Example            |
| ----- | --------------------------------------------------------- | ------------------ |
| email | The email associated with the GCP service account or user | <user@example.com> |

For additional information about GCP Identity Tokens, see [Google Cloud Identity](https://cloud.google.com/docs/authentication/get-id-token).

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
* [GCP Identity Token](../client-workloads/identification/gcp-identity-token.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
