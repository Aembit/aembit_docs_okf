---
type: reference
title: "POST /api/v1/access-scenarios/check-overlaps"
description: "Check Access Scenario policy overlaps"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# POST /api/v1/access-scenarios/check-overlaps

Inspect left-side (Client -> MCP Identity Gateway) and right-side (MCP Identity Gateway -> Server) policy overlaps for an Access Scenario.

**Operation ID:** `check-access-scenario-overlaps`

**Parameters:**

- **excludeScenarioId** (query) *(optional)*: string (uuid)
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CreateScenarioDTO

- `application/json`: [CreateScenarioDTO](../schemas/create-scenario-dto.md)

**Responses:**

- **200**: Access Scenario Overlap Check Result
  - `application/json`: [ScenarioOverlapCheckResultDTO](../schemas/scenario-overlap-check-result-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-scenarios/check-overlaps?excludeScenarioId=example-value"
```
