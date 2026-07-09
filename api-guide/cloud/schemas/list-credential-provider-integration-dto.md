---
type: reference
title: "ListCredentialProviderIntegrationDTO"
description: "Page of Credential Provider Integrations"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# ListCredentialProviderIntegrationDTO

Page of Credential Provider Integrations

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
- **url** *(optional)*: string | null
- **type** *(optional)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
- **status** *(optional)*: string | null
- **lastOperationTimestamp** *(optional)*: string (date-time) | null
