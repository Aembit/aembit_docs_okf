---
type: how-to
title: "Certificate Signed Attestation Trust Provider"
description: "How to configure a Certificate Signed Attestation Trust Provider"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/certificate-signed-attestation-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Certificate Signed Attestation Trust Provider

The Certificate Signed Attestation Trust Provider verifies cryptographically signed

attestation documents using PEM certificates that you register. An external system generates and signs the attestation document, and this Trust Provider validates the signature to confirm the document’s authenticity and integrity.

## Key capabilities

**Custom attestation document verification** - Verify signed attestation documents from any system that produces them, without depending on cloud-specific metadata services.

**Support for multiple signing certificates** - Register more than one signing certificate in a single Trust Provider. This supports certificate rotation and environments where different systems use different signing keys.

**Zero-downtime certificate rotation** - Add a new certificate before removing the old one. Both certificates remain valid during the transition period, so attestation continues without interruption.

## Match rules

The Certificate Signed Attestation Trust Provider doesn’t support match rules. This Trust Provider validates only the cryptographic signature of the attestation document, confirming that a trusted certificate signed it. Match rules for filtering on specific attributes within the attestation document aren’t yet available.

## How the Certificate Signed Attestation Trust Provider works

The authentication process involves an external system that generates a signed attestation document and Aembit Cloud that verifies it.

1. An external system generates an attestation document containing metadata about the workload. The system signs this document using a private key from a certificate that you manage.

2. The Aembit Agent Proxy presents the signed attestation document to

   Aembit Cloud as part of the workload authentication flow.

3. Aembit Cloud verifies the document’s signature using the PEM certificate registered in this Trust Provider. If the signature is invalid or the signing certificate doesn’t match any registered certificate, Aembit rejects the request.

4. If the signature is valid, Aembit authenticates the workload and evaluates the configured Access Policy.

## Configure a Certificate Signed Attestation Trust Provider

![Certificate Signed Attestation Trust Provider form](https://docs.aembit.io/src/assets/images/certificate-signed-attestation-trust-provider.png)

1. Log in to your Aembit Tenant.

2. In the sidebar, click **Trust Providers**.

3. Click **+ New**.

4. Enter a **Name** and optional **Description**.

5. From the **Trust Provider** dropdown, select **Certificate Signed Attestation**.

6. Click **+ Add** to add a signing certificate.

7. In the **Certificate** field, paste the PEM-encoded public certificate used to sign attestation documents.

   After you paste the certificate, Aembit automatically populates the **Thumbprint**, **Subject**, and **Expires At** fields. Use these fields to verify you added the correct certificate.

8. Click **Save**.

> **Adding multiple certificates**
>
> To support certificate rotation or multiple signing systems, click **+ Add** again to register additional certificates. Each certificate appears as a row in the certificates table with its **Name**, **Subject**, and **Expires At** values displayed.

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
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
