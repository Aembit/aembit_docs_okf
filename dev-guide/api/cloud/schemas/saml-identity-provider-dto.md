---
type: reference
title: "SamlIdentityProviderDTO"
description: "Individual SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# SamlIdentityProviderDTO

**Extends:** [SSOIdentityProviderDTO](sso-identity-provider-dto.md)

Individual SSO Identity Provider

**Type:** object

**Properties:**

- **type** *(required)*: string - Type of the remote SSO Identity Provider (e.g. SAMLv2 (default) or OIDCv1)
- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **ssoStatementRoleMappings** *(optional)*: null,array - Collection of mappings of SAML attributes to Aembit roles
- **userAccessEnabled** *(optional)*: boolean - Whether user access via this Identity Provider is enabled
- **entityId** *(optional)*: null,string - SAML Entity ID of the remote SSO Identity Provider
- **metadataUrl** *(optional)*: null,string - Metadata URL of the remote SSO Identity Provider
- **metadataXml** *(optional)*: null,string - Metadata XML content of the remote SSO Identity Provider
- **serviceProviderEntityId** *(optional)*: null,string
- **serviceProviderSsoUrl** *(optional)*: null,string
