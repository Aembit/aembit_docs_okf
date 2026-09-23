---
type: reference
title: "GET /api/v1/agent-controllers/{id}"
description: "Get an Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["agent-controller"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/agent-controllers/{id}

Get an Agent Controller identified by its ID.

**Operation ID:** `get-agent-controller`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Agent Controller

**Responses:**

- **200**: Agent Controller
  - `application/json`: [AgentControllerDTO](../schemas/agent-controller-dto.md)
- **204**: Agent Controller Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/agent-controllers/{id}"
```
