---
type: reference
title: "CPTypeAzureEntraFederationV2DTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CPTypeAzureEntraFederationV2DTO

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
- **audience** *(required)*: string - Audience for the access token
- **subject** *(required)*: string - Subject for the access token
- **scope** *(required)*: string - Scope for the access token
- **azureTenant** *(required)*: string - Azure tenant ID
- **clientId** *(required)*: string - Azure client ID
