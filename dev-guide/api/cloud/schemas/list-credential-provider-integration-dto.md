---
type: reference
title: "ListCredentialProviderIntegrationDTO"
description: "Page of Credential Provider Integrations"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# ListCredentialProviderIntegrationDTO

Page of Credential Provider Integrations

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
- **url** *(optional)*: null,string
- **type** *(optional)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
- **status** *(optional)*: null,string
- **lastOperationTimestamp** *(optional)*: null,string (date-time)
