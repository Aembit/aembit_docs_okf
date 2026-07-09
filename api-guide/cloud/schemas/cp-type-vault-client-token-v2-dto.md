---
type: reference
title: "CPTypeVaultClientTokenV2DTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CPTypeVaultClientTokenV2DTO

**Extends:** [CredentialProviderV2DTO](credential-provider-v2-dto.md)

**Type:** object

**Properties:**

- **type** *(required)*: string
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
- **lifetimeTimeSpanSeconds** *(optional)*: integer (int32)
- **lifetimeExpiration** *(optional)*: string (date-time) | null
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Credential Provider
- **issuer** *(required)*: string (https://your_tenant_id.id.aembit.io) - Issuer of the Access Token
- **subject** *(required)*: string - Subject of the Access Token
- **subjectType** *(optional)*: string | null - Subject type, Accepted values: 'literal', 'dynamic'
- **lifetime** *(required)*: integer (int32) - Access Token(used for authentication against vault OIDC provider) Lifetime in seconds
- **customClaimNames** *(optional)*: Array of [JWTClaimDTO](jwt-claim-dto.md) - Custom Claims that are added to the Access Token
- **vaultHost** *(required)*: string - Vault host
- **tls** *(optional)*: boolean - Tls enabled/disabled when connecting to the vault instance
- **port** *(required)*: integer (int32) - Port number for connecting to the vault instance
- **authenticationPath** *(required)*: string - Vault authentication path
- **namespace** *(optional)*: string | null - Namespace to be used when connecting to the vault instance
- **role** *(optional)*: string | null - User role to be used when connecting to the vault instance
- **forwardingConfig** *(optional)*: string | null - Forwarding configuration for the vault request. Accepted values: '', 'conditional', 'unconditional'
- **privateNetworkAccess** *(optional)*: boolean - Specifies whether the Vault instance is accessible over a private network
