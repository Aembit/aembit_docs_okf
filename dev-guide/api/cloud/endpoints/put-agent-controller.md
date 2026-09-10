---
type: reference
title: "PUT /api/v1/agent-controllers"
description: "Update an Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["agent-controller"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/agent-controllers

Update an Agent Controller.

**Operation ID:** `put-agent-controller`

**Request Body:**

AgentControllerDTO

- `application/json`: [AgentControllerDTO](../schemas/agent-controller-dto.md)

**Responses:**

- **200**: Updated Agent Controller
  - `application/json`: [AgentControllerDTO](../schemas/agent-controller-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/agent-controllers"
```
