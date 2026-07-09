---
type: how-to
title: "About the JWT-SVID Token Credential Provider"
description: "This page describes the JWT-SVID Token Credential Provider and how it works"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/about-spiffe-jwt-svid/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2025-08-26T11:29:07-07:00
type_inferred: true
---

# About the JWT-SVID Token Credential Provider

The JSON Web Token-SPIFFE Verifiable Identity Document (JWT-SVID) Token Credential Provider enables secure identity token generation that complies with the [SPIFFE (Secure Production Identity Framework for Everyone)](https://spiffe.io) standard. This Credential Provider functions similarly to the [OIDC ID Token Credential Provider](about-oidc-id-token.md), but enforces SPIFFE-specific requirements for the subject claim format.

By leveraging Aembit’s Trust Provider attestation and credential management capabilities, the JWT-SVID Token Credential Provider generates JWT-formatted tokens that follow the [SPIFFE JWT-SVID specification](https://spiffe.io/docs/latest/keyless/). This allows Client Workloads to authenticate with SPIFFE-aware systems without running separate SPIRE infrastructure.

The JWT-SVID Token Credential Provider supports:

* SPIFFE-compliant subject format (must start with `spiffe://`)
* Dynamic or literal SPIFFE ID configuration
* Standard signing algorithms (RS256 and ES256)
* Custom claims for enhanced identity context
* JWKS endpoint for token verification
* Automatic issuer URL generation based on tenant

See [Create a JWT-SVID Token Credential Provider](spiffe-jwt-svid.md) to create one.

## Common use cases

[Section titled “Common use cases”](#common-use-cases)

* **Service Mesh Authentication** - Securely authenticate workloads in SPIFFE-compliant service meshes like Istio, Consul, or Kuma.
* **Zero Trust Architecture** - Implement Zero Trust identity standards for workload-to-workload communication.
* **SPIFFE-Aware Systems** - Integrate with any system that validates SPIFFE JWT-SVIDs using standard SPIFFE libraries.
* **Managed Identity** - Replace self-managed SPIRE deployments with Aembit’s managed identity issuance.

## How the JWT-SVID Token Credential Provider works

[Section titled “How the JWT-SVID Token Credential Provider works”](#how-the-jwt-svid-token-credential-provider-works)

1. **Token Generation** - Aembit generates SPIFFE-compliant JWT-SVID tokens and signs them using your Aembit Tenant-specific keys. The tokens follow the SPIFFE JWT-SVID specification and include standard claims (`exp`, `iat`, `jti`) along with any configured custom claims.

2. **SPIFFE ID Configuration** - Aembit sets the subject claim using your configured SPIFFE ID:

   * **Literal** - Uses a fixed SPIFFE ID value that you provide (must start with `spiffe://`)
   * **Dynamic** - Derives the SPIFFE ID from workload attributes using variables

   The UI displays a warning if the subject doesn’t follow SPIFFE format requirements.

3. **Token Verification** - SPIFFE-aware downstream systems verify the JWT-SVID using Aembit’s JWKS endpoint, which publishes the public keys needed for signature validation.

## Configuration options

[Section titled “Configuration options”](#configuration-options)

The following sections detail the configuration options you have for the JWT-SVID Token Credential Provider:

### SPIFFE ID configuration

[Section titled “SPIFFE ID configuration”](#spiffe-id-configuration)

SPIFFE IDs uniquely identify workloads within a trust domain and follow this format:

```text
spiffe://<trust_domain>/<workload_path>
```

Aembit supports multiple strategies for SPIFFE ID generation:

* **Dynamic Generation** - Automatically derives SPIFFE IDs from existing workload attributes:

  * Kubernetes: `spiffe://your-domain/ns/${namespace}/sa/${serviceaccount}`
  * AWS: `spiffe://your-domain/aws/account/${account}/role/${role}`
  * Custom patterns using workload identity attributes

* **Literal Configuration** - Set a fixed SPIFFE ID for specific use cases where dynamic generation isn’t suitable

### Issuer configuration

[Section titled “Issuer configuration”](#issuer-configuration)

The issuer URL identifies the entity that created and signed the JWT-SVID:

* Automatically generated based on your Aembit tenant configuration
* Follows the format: `https://<your-tenant>.aembit.com`
* Used by relying parties to verify the token’s origin

### Claims configuration

[Section titled “Claims configuration”](#claims-configuration)

Configure standard and custom claims in your JWT-SVIDs:

**Standard SPIFFE claims (automatically managed):**

* `sub` - SPIFFE ID of the workload
* `iss` - Issuer URL automatically generated based on your tenant
* `aud` - Audience claim (single string)
* `exp` - Token expiration time
* `iat` - Token issued at time
* `jti` - Unique token identifier

**Custom claims support:**

* Add workload-specific metadata
* Include environment context
* Pass authorization attributes
* Support for both literal and dynamic claim values

For detailed syntax and examples of dynamic claims, see [Dynamic Claims for OIDC and JWT-SVID Tokens](advanced-options/dynamic-claims-oidc.md).

### Signing configuration

[Section titled “Signing configuration”](#signing-configuration)

Aembit manages signing keys and algorithms according to SPIFFE standards:

* **Algorithm support:**

  * **RS256** (RSASSA-PKCS1-v1\_5 using SHA-256) - Default, widely compatible
  * **ES256** (ECDSA using P-256 and SHA-256) - Recommended for SPIFFE-compliant systems

* **Key management:**

  * Automatic key rotation
  * Separate keys per algorithm type
  * Published via standard JWKS endpoint

### JWKS endpoint

[Section titled “JWKS endpoint”](#jwks-endpoint)

Aembit exposes a public JWKS endpoint for JWT-SVID verification:

* Standards-compliant formatting compatible with SPIFFE libraries
* Includes all active public keys
* Supports key rotation without service disruption
* Available at: `https://<your-tenant>.aembit.com/.well-known/jwks.json`

## Implementation notes

[Section titled “Implementation notes”](#implementation-notes)

* The Credential Provider generates SPIFFE-compliant JWT-SVID tokens without requiring separate SPIRE infrastructure.
* Current implementation supports ES256 and RS256 signing algorithms as specified by the SPIFFE standard.
* Aembit recommends testing JWT-SVID validation with SPIFFE SDK libraries before production deployment.

## Common SPIFFE JWT-SVID claims

[Section titled “Common SPIFFE JWT-SVID claims”](#common-spiffe-jwt-svid-claims)

The following table describes standard SPIFFE JWT-SVID claims and their configuration:

| Claim             | Description                                      | Type            | Configuration Examples                                                                                                            |
| ----------------- | ------------------------------------------------ | --------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `sub`             | **Subject** - SPIFFE ID of the workload          | Dynamic/Literal | **Dynamic**: `spiffe://example.com/ns/${namespace}/sa/${serviceaccount}` **Literal**: `spiffe://example.com/workload/api-service` |
| `iss`             | **Issuer** - Trust domain-based issuer URL       | Auto-generated  | Automatically set based on trust domain configuration                                                                             |
| `aud`             | **Audience** - Target system expecting the token | Literal         | Single: `my-service.example.com`                                                                                                  |
| `exp`             | **Expiration** - Token validity end time         | Auto-generated  | Set via **Lifetime** field in minutes (for example, `60` for `1` hour)                                                            |
| `iat`             | **Issued At** - Token creation timestamp         | Auto-generated  | Automatically set by Aembit upon token issuance                                                                                   |
| `jti`             | **JWT ID** - Unique token identifier             | Auto-generated  | Automatically generated to prevent replay attacks                                                                                 |
| `namespace`       | **Namespace** - Kubernetes namespace             | Dynamic         | `${oidc.identityToken.decode.payload.namespace}`                                                                                  |
| `service_account` | **Service Account** - Kubernetes service account | Dynamic         | `${oidc.identityToken.decode.payload.service_account}`                                                                            |
| `aws_account`     | **AWS Account** - AWS account ID                 | Dynamic         | `${aws.account}`                                                                                                                  |
| `environment`     | **Environment** - Deployment environment         | Literal/Dynamic | **Literal**: `production` **Dynamic**: `${os.environment.ENV}`                                                                    |

For more information on using dynamic expressions in these claims, see [Dynamic Claims for OIDC and JWT-SVID Tokens](advanced-options/dynamic-claims-oidc.md).

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [SPIFFE JWT-SVID Specification](https://spiffe.io/docs/latest/keyless/)
* [How to Construct SPIFFE IDs](https://www.spirl.com/blog/how-to-construct-spiffe-ids)
* [SPIFFE Standards Documentation](https://spiffe.io/docs/latest/spiffe-about/spiffe-concepts/)
