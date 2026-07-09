---
type: how-to
title: "SAMLv2 Response Trust Provider"
description: "How to configure a SAMLv2 Response Trust Provider"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/saml-response-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2026-03-19T13:39:29-07:00
type_inferred: true
---

# SAMLv2 Response Trust Provider

Use the SAMLv2 Response Trust Provider to validate SAML 2.0 responses from your identity provider (IdP).

## Match rules

[Section titled “Match rules”](#match-rules)

The following table describes the match rules available for SAMLv2 Response Trust Providers:

| Rule\Claim       | Description                                                                                                                                                                                                                        |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Audience (`aud`) | Identifies who the SAML 2.0 response is **for**. It specifies the intended recipient (Service Provider), ensuring a response created for one service isn’t misused for another. *Example*: `https://aembit.example.com/saml/acs`   |
| Issuer (`iss`)   | Identifies who **issued** the SAML 2.0 response. It’s the entity ID of the identity provider that you are trusting. This verifies the response came from the correct source. *Example*: `https://identity-provider.my-company.com` |
| Subject (`sub`)  | Identifies **who** the SAML 2.0 response is about. It’s the NameID value that represents the specific principal (user or service account) Aembit is to authenticate. *Example*: `user@my-company.com`                              |

## Attestation configuration

[Section titled “Attestation configuration”](#attestation-configuration)

The SAMLv2 Response Trust Provider validates SAML 2.0 responses using your IdP’s signing certificate. Aembit retrieves the signing certificate from the IdP’s SAML metadata. You can provide this metadata via a URL or an XML file.

Note

You must use either the Metadata URL method or Metadata XML method, not both.

| Configuration method | Description                                                                                                                                                                                                                                                                                                                 |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Metadata URL         | The URL to your identity provider’s SAML 2.0 metadata endpoint. Aembit fetches the metadata automatically and extracts the signing certificate. Use this when you want the Trust Provider to track whatever the IdP publishes at its metadata endpoint. *Example*: `https://identity-provider.my-company.com/saml/metadata` |
| Metadata XML         | The raw SAML 2.0 metadata XML from your identity provider. Paste the full XML content into this field. Use this when the metadata endpoint isn’t reachable from Aembit Cloud, or when you want explicit control over the metadata content.                                                                                  |

## How the SAMLv2 Response Trust Provider works

[Section titled “How the SAMLv2 Response Trust Provider works”](#how-the-samlv2-response-trust-provider-works)

The authentication process involves your identity provider, the user’s browser, and Aembit.

1. A user authenticates with your SAML 2.0 identity provider through their browser. The IdP generates a signed SAML 2.0 response containing assertions about the user’s identity.

2. The user’s browser presents the SAML 2.0 response to Aembit as part of the MCP Authorization Server authentication flow.

3. Aembit validates the SAML 2.0 response’s signature using the signing certificate from the configured metadata (fetched from the Metadata URL or extracted from the Metadata XML). If the signature is invalid, Aembit rejects the request.

4. If the signature is valid, Aembit validates the response’s claims. Aembit compares the audience, issuer, and subject values in the SAML 2.0 response against the match rules you configured.

5. If the signature and all claims are valid, Aembit authenticates the user and applies the relevant Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md). If any check fails, Aembit denies the request.

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

* [Redirect URI](../client-workloads/identification/redirect-uri.md)
