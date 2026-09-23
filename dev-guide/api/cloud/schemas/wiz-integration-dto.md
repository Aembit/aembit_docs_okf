---
type: reference
title: "WizIntegrationDTO"
description: "Integration details for 3rd party data used by Access Conditions"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# WizIntegrationDTO

**Extends:** [IntegrationV2DTO](integration-v2-dto.md)

Integration details for 3rd party data used by Access Conditions

**Type:** object

**Properties:**

- **integrationType** *(required)*: null,string
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
- **accessConditionsCount** *(optional)*: integer (int32)
- **clientId** *(optional)*: null,string
- **tokenUrl** *(optional)*: null,string
- **clientSecret** *(optional)*: null,string
- **audience** *(optional)*: null,string
