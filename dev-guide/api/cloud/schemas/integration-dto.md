---
type: reference
title: "IntegrationDTO"
description: "Integration details for 3rd party data used by Access Conditions"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# IntegrationDTO

Integration details for 3rd party data used by Access Conditions

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
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **type** *(required)*: string
- **syncFrequencySeconds** *(required)*: integer (int32)
- **lastSync** *(optional)*: null,string (date-time)
- **lastSyncStatus** *(optional)*: null,string
- **endpoint** *(required)*: string
- **integrationJSON** *(required)*: object
- **accessConditionsCount** *(optional)*: integer (int32)
