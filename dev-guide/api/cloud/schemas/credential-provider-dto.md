---
type: reference
title: "CredentialProviderDTO"
description: "Individual Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CredentialProviderDTO

Individual Credential Provider

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **type** *(required)*: string - Credential Provider Type (e.g. oauth-client-credential, username-password, etc.)
- **roleId** *(optional)*: null,string (uuid) - Credential Provider Role for use with Aembit Access Token type Credential Providers
- **lifetimeTimeSpanSeconds** *(optional)*: integer (int32) - The Lifetime of a Credential Provider's credential value
- **lifetimeExpiration** *(optional)*: null,string (date-time) - The expiration timestamp for a Credential Provider's credential value
- **providerDetailJSON** *(optional)*: null,string - JSON representation of the Credential Provider configuration details
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Credential Provider
