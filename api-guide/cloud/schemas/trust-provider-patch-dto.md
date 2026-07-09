---
type: reference
title: "TrustProviderPatchDTO"
description: "Patch request for an individual Trust Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# TrustProviderPatchDTO

Patch request for an individual Trust Provider

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - New Name for the identified entity
- **description** *(optional)*: string | null - New Description for the identified entity
- **isActive** *(optional)*: boolean (boolean) | null - New Status for the identified entity
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md) - New Tags for the identified entity
- **provider** *(optional)*: string | null - Trust Provider Type
- **matchRules** *(optional)*: Array of [TrustProviderMatchRuleDTO](trust-provider-match-rule-dto.md) - Trust Provider Match Rules
- **oidcUrl** *(optional)*: string | null - OIDC URL to use for retrieving JWKS Public Keys
- **pemType** *(optional)*: string | null - PEM Input Type
- **certificate** *(optional)*: string | null - Trust Provider Certificate or Public Key for cryptographic attestation
- **jwks** *(optional)*: string | null - Jwks Content for cryptographic attestation
- **symmetricKey** *(optional)*: string | null - Symmetric Key
- **publicKeyValidation** *(optional)*: [PublicKeyValidationDTO](public-key-validation-dto.md)
