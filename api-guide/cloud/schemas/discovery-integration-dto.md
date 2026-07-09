---
type: reference
title: "DiscoveryIntegrationDTO"
description: "Integration details for 3rd party data used by Discovery"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# DiscoveryIntegrationDTO

Integration details for 3rd party data used by Discovery

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
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **type** *(required)*: string
- **syncFrequencySeconds** *(required)*: integer (int32)
- **lastSync** *(optional)*: string (date-time) | null
- **lastSyncStatus** *(optional)*: string | null
- **endpoint** *(required)*: string
- **discoveryIntegrationJSON** *(required)*: string
