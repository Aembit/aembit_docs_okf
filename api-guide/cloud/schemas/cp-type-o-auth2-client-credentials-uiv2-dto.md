---
type: reference
title: "CPTypeOAuth2ClientCredentialsUIV2DTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CPTypeOAuth2ClientCredentialsUIV2DTO

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
- **clientID** *(required)*: string - OAuth Client ID 
- **clientSecret** *(optional)*: string | null - OAuth Client Secret
- **scope** *(optional)*: string | null - OAuth Scopes
- **customParameters** *(optional)*: Array of [CPTypeOAuth2CustomParameters](cp-type-o-auth2-custom-parameters.md) - Custom Claims that are added to the Access Token
- **url** *(required)*: string - OAuth Token URL for handling token requests
- **credentialStyle** *(optional)*: string | null - Defines how credential would be transmitted. Accepted value: 'postBody', 'authHeader'
