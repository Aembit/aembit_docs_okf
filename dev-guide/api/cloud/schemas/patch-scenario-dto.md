---
type: reference
title: "PatchScenarioDTO"
description: "Patch Access Scenario"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# PatchScenarioDTO

Patch Access Scenario

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **type** *(optional)*: null,string - Access Scenario Type
- **isProcessed** *(optional)*: null,boolean - Processed Status
- **accessPolicies** *(optional)*: null,array - Access Policies associated with this Scenario
- **client** *(optional)*: [ScenarioClientDTO](scenario-client-dto.md) - Client Workload configuration for Scenario orchestration
- **humanContext** *(optional)*: [ScenarioHumanContextDTO](scenario-human-context-dto.md) - Human Context configuration for Scenario orchestration
- **targets** *(optional)*: null,array - Target Server configurations for Scenario orchestration
- **gatewayClientWorkloadId** *(optional)*: null,string (uuid) - MCP Identity Gateway Client Workload ID
- **gatewayServerWorkloadId** *(optional)*: null,string (uuid) - MCP Identity Gateway Server Workload ID
- **gatewayHost** *(optional)*: null,string - MCP Identity Gateway Host
