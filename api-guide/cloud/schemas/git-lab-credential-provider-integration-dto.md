---
type: reference
title: "GitLabCredentialProviderIntegrationDTO"
description: "Individual Credential Provider Integration"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# GitLabCredentialProviderIntegrationDTO

**Extends:** [CredentialProviderIntegrationDTO](credential-provider-integration-dto.md)

Individual Credential Provider Integration

**Type:** object

**Properties:**

- **type** *(required)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
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
- **tokenExpiration** *(optional)*: string (date-time) | null
- **lastOperationTimestamp** *(optional)*: string (date-time) | null
- **status** *(optional)*: string | null
- **errorMessage** *(optional)*: string | null
- **url** *(required)*: string
- **userId** *(optional)*: string | null
- **topLevelGroupId** *(optional)*: string | null
- **personalAccessToken** *(optional)*: string | null
