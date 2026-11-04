---
type: reference
title: "PATCH /api/v1/access-scenarios/{id}"
description: "Patch an Access Scenario"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-scenario"]
timestamp: 2026-11-03T18:06:18-08:00
---

# PATCH /api/v1/access-scenarios/{id}

Patch an Access Scenario identified by its ID.

**Operation ID:** `patch-access-scenario`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Scenario
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

PatchScenarioDTO

- `application/json`: [PatchScenarioDTO](../schemas/patch-scenario-dto.md)

**Responses:**

- **200**: Patched Access Scenario
  - `application/json`: [GetScenarioDTO](../schemas/get-scenario-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Access Scenario Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-scenarios/{id}"
```
