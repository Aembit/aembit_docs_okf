---
type: reference
title: "CredentialProviderUIDTO"
description: "Individual Credential Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CredentialProviderUIDTO

Individual Credential Provider

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
- **type** *(required)*: string - Credential Provider Type (e.g. oauth-client-credential, username-password, etc.)
- **roleId** *(optional)*: string (uuid) | null - Credential Provider Role for use with Aembit Access Token type Credential Providers
- **lifetimeTimeSpanSeconds** *(optional)*: integer (int32) - The Lifetime of a Credential Provider's credential value
- **lifetimeExpiration** *(optional)*: string (date-time) | null - The expiration timestamp for a Credential Provider's credential value
- **providerDetailJSON** *(optional)*: string | null - JSON representation of the Credential Provider configuration details
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Credential Provider
