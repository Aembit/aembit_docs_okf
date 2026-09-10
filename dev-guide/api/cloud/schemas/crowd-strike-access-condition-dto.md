---
type: reference
title: "CrowdStrikeAccessConditionDTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CrowdStrikeAccessConditionDTO

**Extends:** [AccessConditionV2DTO](access-condition-v2-dto.md)

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
- **integrationID** *(optional)*: string (uuid) - ID of the Integration Entity used by this Access Condition
- **integration** *(optional)*: null - Integration Entity used by this Access Condition
- **maxLastSeenSeconds** *(optional)*: integer (int32)
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Access Condition
- **matchLocalIP** *(optional)*: boolean
- **matchHostname** *(optional)*: boolean
- **matchMacAddress** *(optional)*: boolean
- **matchSerialNumber** *(optional)*: boolean
- **preventRestrictedFunctionalityMode** *(optional)*: boolean
