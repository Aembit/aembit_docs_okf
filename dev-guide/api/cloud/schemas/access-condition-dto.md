---
type: reference
title: "AccessConditionDTO"
description: "DTO of an individual Access Condition for enforcement during Access Policy evaluation"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AccessConditionDTO

DTO of an individual Access Condition for enforcement during Access Policy evaluation

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
- **integrationID** *(optional)*: string (uuid) - ID of the Integration Entity used by this Access Condition
- **integration** *(optional)*: [IntegrationDTO](integration-dto.md) - Integration Entity used by this Access Condition
- **conditions** *(required)*: object - Rules which are enforced by the Access Condition
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Access Condition
- **integrationType** *(optional)*: null,string
