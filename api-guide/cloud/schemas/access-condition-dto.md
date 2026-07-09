---
type: reference
title: "AccessConditionDTO"
description: "DTO of an individual Access Condition for enforcement during Access Policy evaluation"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AccessConditionDTO

DTO of an individual Access Condition for enforcement during Access Policy evaluation

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
- **integrationID** *(optional)*: string (uuid) - ID of the Integration Entity used by this Access Condition
- **integration** *(optional)*: [IntegrationDTO](integration-dto.md)
- **conditions** *(required)*: object - Rules which are enforced by the Access Condition
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Access Condition
- **integrationType** *(optional)*: string | null
