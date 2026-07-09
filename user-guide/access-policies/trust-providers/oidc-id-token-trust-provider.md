---
type: how-to
title: "OIDC ID Token Trust Provider"
description: "How to configure an OIDC ID Token Trust Provider"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/oidc-id-token-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2026-03-23T14:00:13-04:00
type_inferred: true
---

# OIDC ID Token Trust Provider

The OIDC ID Token Trust Provider is Aembit’s solution for authenticating workloads using standard OIDC ID tokens. It validates incoming tokens against specific claims, including the following:

* issuer
* audience
* subject
* any custom claims you define

This gives you maximum flexibility to integrate with virtually any OIDC-compliant identity provider for secure, token-based workload access.

## Benefits

[Section titled “Benefits”](#benefits)

By supporting the open OIDC standard, Aembit provides you with maximum flexibility and the following benefits:

**Support for Any OIDC Provider** - Connect to any identity provider compliant with the OIDC standard.

**Reduced Static Credentials** - Replace static credentials with short-lived OIDC tokens for more workloads.

**Standardized Integration** - Avoid custom development for new tools that support OIDC.

**Simplified Operations** - Apply a single authentication pattern for all OIDC-enabled workloads.

## Match rules

[Section titled “Match rules”](#match-rules)

The following table describes the match rules available for the OIDC ID Token Trust Provider:

| Rule\Claim       | Description                                                                                                                                                                                                                                                                                                                                                                                                |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Audience (`aud`) | Identifies who the token is **for**. It specifies the intended recipient, ensuring a token created for one purpose isn’t misused for another. This helps prevent token replay attacks. *Example*: `aembit-prod-api-access`                                                                                                                                                                                 |
| Issuer (`iss`)   | Identifies who **issued** the token. It’s the URL of the identity provider system (such as Okta, GitLab, Jenkins) that you are trusting. This verifies the token came from the correct source. *Example*: `https://identity-provider.my-company.com`                                                                                                                                                       |
| Subject (`sub`)  | Identifies **what or who** the token is about. It’s a unique, case-sensitive string that represents the specific principal (a workload, service, or service account) Aembit is to authenticate. *Example*: `workload-id-98765` or `user-id-xyz-123`                                                                                                                                                        |
| Custom Claim     | Matches on **any additional claim** present in the OIDC ID token. You define both the claim name and its expected value, allowing you to create match rules for provider-specific or application-specific claims beyond the standard `aud`, `iss`, and `sub` claims. You can add multiple custom claim match rules to a single Trust Provider. *Example*: Claim Name: `store_number`, Claim Value: `12345` |

## Attestation methods

[Section titled “Attestation methods”](#attestation-methods)

The following table describes the attestation methods available for the OIDC ID Token Trust Provider:

| Attestation Method | Description                                                                                                                                                                                                                                                                                                                                                                   |
| ------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| OIDC Discovery     | Aembit uses this address to automatically find the provider’s configuration and locate its public keys. Use this for standard OIDC providers where you want to simplify configuration to a single URL. You must enter the main URL of the identity provider (like `https://gitlab.com`).                                                                                      |
| Symmetric Key      | A single shared secret key. Your OIDC provider uses this key to sign tokens, and Aembit uses the same key to validate them. Symmetric algorithms like HS256 use this method. Use this attestation method for closed systems or legacy services where a shared secret works better than public key cryptography. You must enter a shared secret key that’s Base64 encoded.     |
| Upload JWKS        | This is a static, point-in-time snapshot of your provider’s JSON Web Key Set (JWKS), which contains their public signing keys. Use this for any provider that exposes a public JWKS endpoint. This is what enables automatic key rotation for you. You must paste the entire JSON content of the provider’s JWKS into this field.                                             |
| Upload Public Key  | Your provider’s public key file (such as `.pem` or `.cer`). Use this for providers in private or air-gapped networks that don’t expose a public JWKS endpoint. You must paste the text content of a single public key, typically in PEM format. The Thumbprint is a unique, short identifier for that key that Aembit automatically calculates and displays for verification. |

## How the OIDC ID Token Trust Provider works

[Section titled “How the OIDC ID Token Trust Provider works”](#how-the-oidc-id-token-trust-provider-works)

The authentication process involves a clear sequence of actions performed by your workload and by Aembit.

1. First, your workload requests an OIDC ID token from its identity provider (such as GitLab, Jenkins). The workload then presents this token to Aembit to prove its identity.

2. Next, Aembit validates the token’s signature. Using the configured Attestation Method, Aembit retrieves the provider’s public key and verifies that the signature is authentic. If the signature is invalid, Aembit rejects the request.

3. If the signature is valid, Aembit then validates the token’s claims. Aembit compares the claims within the token against the Match Rules you configured, including the issuer, audience, subject, and any custom claims.

4. If the signature and all claims are valid, Aembit authenticates the workload and applies the relevant access policies. If any check fails, Aembit denies the request.

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
* [OIDC ID Token](../client-workloads/identification/oidc-id-token.md)
* [OIDC ID Token Audience](../client-workloads/identification/oidc-id-token-audience.md)
* [OIDC ID Token Issuer](../client-workloads/identification/oidc-id-token-issuer.md)
* [OIDC ID Token Subject](../client-workloads/identification/oidc-id-token-subject.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Redirect URI](../client-workloads/identification/redirect-uri.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
