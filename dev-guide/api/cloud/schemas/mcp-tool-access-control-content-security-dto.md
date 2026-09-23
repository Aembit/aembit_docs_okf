---
type: reference
title: "McpToolAccessControlContentSecurityDTO"
description: "Individual Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# McpToolAccessControlContentSecurityDTO

**Extends:** [ContentSecurityDTO](content-security-dto.md)

Individual Content Security

**Type:** object

**Properties:**

- **type** *(required)*: string - Content Security Type
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
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Content Security
- **mode** *(required)*: [ToolVisibilityMode](tool-visibility-mode.md)
- **visibility** *(required)*: [ToolRuleBehaviour](tool-rule-behaviour.md)
- **invocation** *(required)*: [ToolRuleBehaviour](tool-rule-behaviour.md)
