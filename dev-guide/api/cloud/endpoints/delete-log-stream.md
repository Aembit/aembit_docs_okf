---
type: reference
title: "DELETE /api/v1/log-streams/{id}"
description: "Delete a Log Stream"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["log-stream"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v1/log-streams/{id}

Delete a Log Stream identified by its ID.

**Operation ID:** `delete-log-stream`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Log Stream

**Responses:**

- **204**: Successfully deleted Log Stream
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/log-streams/{id}"
```
