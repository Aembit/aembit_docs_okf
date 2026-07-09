---
type: reference
title: "TrustProviderDTO"
description: "Individual Trust Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# TrustProviderDTO

Individual Trust Provider

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: string | null - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md)
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: string (date-time) | null
- **createdBy** *(optional)*: string | null
- **modifiedBy** *(optional)*: string | null
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **id** *(optional)*: integer (int32) - Trust Provider Id
- **provider** *(required)*: string - Trust Provider Type
- **matchRules** *(optional)*: Array of [TrustProviderMatchRuleDTO](trust-provider-match-rule-dto.md) - Trust Provider Match Rules
- **certificate** *(optional)*: string | null - Trust Provider Certificate or Public Key for cryptographic attestation
- **jwks** *(optional)*: string | null - Jwks Content for cryptographic attestation
- **publicKeyValidation** *(optional)*: [PublicKeyValidationDTO](public-key-validation-dto.md)
- **oidcUrl** *(optional)*: string | null - OIDC URL to use for retrieving JWKS Public Keys
- **symmetricKey** *(optional)*: string | null - Symmetric Key
- **pemType** *(optional)*: string | null - PEM Input Type
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Trust Provider
- **agentControllersCount** *(optional)*: integer (int32) - Agent Controllers associated with this Trust Provider
- **agentControllerIds** *(optional)*: Array of string (uuid) - Agent Controller IDs associated with this Trust Provider
