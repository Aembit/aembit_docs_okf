---
type: reference
title: "CPTypeOAuth2ClientCredentialsUIV2DTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# CPTypeOAuth2ClientCredentialsUIV2DTO

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
- **clientID** *(required)*: string - OAuth Client ID 
- **clientSecret** *(optional)*: null,string - OAuth Client Secret
- **scope** *(optional)*: null,string - OAuth Scopes
- **customParameters** *(optional)*: null,array - Custom Claims that are added to the Access Token
- **url** *(required)*: string - OAuth Token URL for handling token requests
- **credentialStyle** *(optional)*: null,string - Defines how credential would be transmitted. Accepted value: 'postBody', 'authHeader'
