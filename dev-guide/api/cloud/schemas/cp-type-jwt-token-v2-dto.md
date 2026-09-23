---
type: reference
title: "CPTypeJWTTokenV2DTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# CPTypeJWTTokenV2DTO

**Extends:** [CredentialProviderV2DTO](credential-provider-v2-dto.md)

**Type:** object

**Properties:**

- **type** *(required)*: string
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
- **lifetimeTimeSpanSeconds** *(optional)*: integer (int32)
- **lifetimeExpiration** *(optional)*: null,string (date-time)
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Credential Provider
- **tokenConfiguration** *(required)*: string - Token configuration type. Accepted value: 'snowflake'
- **lifetime** *(required)*: integer (int32) - Access token lifetime (in seconds)
- **algorithmType** *(required)*: string - Token signing algorithm. Accepted value: 'RS256'
- **issuer** *(required)*: string (Snowflake_Account_Name.Snowflake_Username.SHA256:{sha256(publicKey)}) - Issuer of the access token
- **subject** *(required)*: string (Snowflake_Account_Name.Snowflake_Username) - Subject of the access token
- **keyContent** *(optional)*: null,string
- **privateKey** *(optional)*: null,string
