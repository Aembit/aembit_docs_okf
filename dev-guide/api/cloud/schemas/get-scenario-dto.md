---
type: reference
title: "GetScenarioDTO"
description: "Individual Scenario"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# GetScenarioDTO

Individual Scenario

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
- **type** *(required)*: string - Access Scenario Type
- **isProcessed** *(optional)*: boolean
- **accessPolicies** *(optional)*: null,array - Access Policies associated with this Scenario
- **client** *(optional)*: [ScenarioClientDTO](scenario-client-dto.md) - Client Workload configuration for Scenario orchestration
- **humanContext** *(optional)*: [ScenarioHumanContextDTO](scenario-human-context-dto.md) - Human Context configuration for Scenario orchestration
- **targets** *(optional)*: null,array - Target Server configurations for Scenario orchestration
- **gatewayClientWorkloadId** *(optional)*: null,string (uuid) - MCP Identity Gateway Client Workload ID
- **gatewayServerWorkloadId** *(optional)*: null,string (uuid) - MCP Identity Gateway Server Workload ID
- **gatewayHost** *(optional)*: null,string - MCP Identity Gateway Host
- **overlaps** *(optional)*: null,array - Detected Policy Overlaps
- **skippedPolicies** *(optional)*: null,array - Policies skipped due to Option A overlap rules
