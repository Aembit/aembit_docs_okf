---
type: reference
title: "POST /api/v1/access-scenarios"
description: "Create an Access Scenario"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# POST /api/v1/access-scenarios

Create an Access Scenario.

**Operation ID:** `post-access-scenario`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CreateScenarioDTO

- `application/json`: [CreateScenarioDTO](../schemas/create-scenario-dto.md)

**Responses:**

- **201**: Created Access Scenario
  - `application/json`: [GetScenarioDTO](../schemas/get-scenario-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-scenarios"
```
