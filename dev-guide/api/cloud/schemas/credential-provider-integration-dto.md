---
type: reference
title: "CredentialProviderIntegrationDTO"
description: "Individual Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CredentialProviderIntegrationDTO

Individual Credential Provider Integration

**Type:** object

**Properties:**

- **type** *(required)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
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
- **tokenExpiration** *(optional)*: null,string (date-time)
- **lastOperationTimestamp** *(optional)*: null,string (date-time)
- **status** *(optional)*: null,string
- **errorMessage** *(optional)*: null,string
