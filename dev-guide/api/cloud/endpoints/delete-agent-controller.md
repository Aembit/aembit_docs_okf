---
type: reference
title: "DELETE /api/v1/agent-controllers/{id}"
description: "Delete an Agent Controller"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["agent-controller"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/agent-controllers/{id}

Delete an Agent Controller identified by its ID.

**Operation ID:** `delete-agent-controller`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Agent Controller

**Responses:**

- **201**: Successfully deleted Agent Controller
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/agent-controllers/{id}"
```
