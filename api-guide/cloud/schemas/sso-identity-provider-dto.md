---
type: reference
title: "SSOIdentityProviderDTO"
description: "Individual SSO Identity Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# SSOIdentityProviderDTO

Individual SSO Identity Provider

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
- **entityId** *(optional)*: string | null - SAML Entity ID of the remote SSO Identity Provider
- **metadataUrl** *(optional)*: string | null - Metadata URL of the remote SSO Identity Provider
- **metadataXml** *(optional)*: string | null - Metadata XML content of the remote SSO Identity Provider
- **samlStatementRoleMappings** *(optional)*: Array of [SamlStatementRoleMappingDTO](saml-statement-role-mapping-dto.md) - Collection of mappings of SAML attributes to Aembit roles
