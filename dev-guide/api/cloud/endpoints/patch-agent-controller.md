---
type: reference
title: "PATCH /api/v1/agent-controllers/{id}"
description: "Patch an Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["agent-controller"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v1/agent-controllers/{id}

Patch an Agent Controller identified by its ID.

**Operation ID:** `patch-agent-controller`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Agent Controller

**Request Body:**

AgentControllerPatchDTO

- `application/json`: [AgentControllerPatchDTO](../schemas/agent-controller-patch-dto.md)

**Responses:**

- **200**: Patched Agent Controller
  - `application/json`: [AgentControllerDTO](../schemas/agent-controller-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/agent-controllers/{id}"
```
