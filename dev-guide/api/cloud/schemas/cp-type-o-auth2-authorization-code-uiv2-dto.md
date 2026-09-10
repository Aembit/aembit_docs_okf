---
type: reference
title: "CPTypeOAuth2AuthorizationCodeUIV2DTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CPTypeOAuth2AuthorizationCodeUIV2DTO

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
- **oAuthUrl** *(required)*: string - OAuth well-known metadata endpoint
- **authorizationUrl** *(required)*: string - OAuth Authorization URL for handling authorization requests
- **tokenUrl** *(required)*: string - OAuth Token URL for handling token requests
- **introspectionUrl** *(optional)*: null,string - Introspection Url of the OAuth 2.0 introspection endpoint, used to validate and obtain metadata about access tokens
- **isPkceRequired** *(optional)*: boolean - Indicates if Proof Key for Code Exchange (PKCE) protocol flow must be used
- **callBackUrl** *(optional)*: null,string () - The callback URL where the Authorization Server sends the Authorization Code
- **finalCallbackUrl** *(optional)*: null,string - Redirect URL after Aembit successfully completes OAuth authorization. Only available to Tenants which are entitled - contact Aembit support if you require this functionality.
- **userAuthorizationUrl** *(optional)*: null,string - Authorization URL to be used for authorization of the Credential Provider by a privileged user
- **state** *(optional)*: null,string - State parameter to maintain state between the authorization request and callback
