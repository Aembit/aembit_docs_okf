---
type: reference
title: "TrustProviderPatchDTO"
description: "Patch request for an individual Trust Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# TrustProviderPatchDTO

Patch request for an individual Trust Provider

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **provider** *(optional)*: null,string - Trust Provider Type
- **matchRules** *(optional)*: null,array - Trust Provider Match Rules
- **oidcUrl** *(optional)*: null,string - OIDC URL to use for retrieving JWKS Public Keys
- **pemType** *(optional)*: null,string - PEM Input Type
- **certificate** *(optional)*: null,string - Trust Provider Certificate or Public Key for cryptographic attestation
- **jwks** *(optional)*: null,string - Jwks Content for cryptographic attestation
- **symmetricKey** *(optional)*: null,string - Symmetric Key
- **publicKeyValidation** *(optional)*: [PublicKeyValidationDTO](public-key-validation-dto.md) - Response to a request for Public Key Validation
- **metadataUrl** *(optional)*: null,string - Metadata URL of the remote SSO Identity Provider
- **metadataXml** *(optional)*: null,string - Metadata XML content of the remote SSO Identity Provider
