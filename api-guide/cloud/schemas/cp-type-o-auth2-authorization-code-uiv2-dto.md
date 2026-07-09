---
type: reference
title: "CPTypeOAuth2AuthorizationCodeUIV2DTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# CPTypeOAuth2AuthorizationCodeUIV2DTO

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
- **oAuthUrl** *(required)*: string - OAuth well-known metadata endpoint
- **authorizationUrl** *(required)*: string - OAuth Authorization URL for handling authorization requests
- **tokenUrl** *(required)*: string - OAuth Token URL for handling token requests
- **introspectionUrl** *(optional)*: string | null - Introspection Url of the OAuth 2.0 introspection endpoint, used to validate and obtain metadata about access tokens
- **isPkceRequired** *(optional)*: boolean - Indicates if Proof Key for Code Exchange (PKCE) protocol flow must be used
- **callBackUrl** *(optional)*: string () | null - The callback URL where the Authorization Server sends the Authorization Code
- **userAuthorizationUrl** *(optional)*: string | null - Authorization URL to be used for authorization of the Credential Provider by a privileged user
- **state** *(optional)*: string | null - State parameter to maintain state between the authorization request and callback
