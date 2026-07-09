---
type: reference
title: "PATCH /api/v1/log-streams/{id}"
description: "Patch a Log Stream"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [log-stream]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/log-streams/{id}

Patch a Log Stream identified by its ID.

**Operation ID:** `patch-log-stream`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Log Stream

**Request Body:**

LogStreamPatchDTO

- `application/json`: [LogStreamPatchDTO](../schemas/log-stream-patch-dto.md)

**Responses:**

- **200**: Patched Log Stream
  - `application/json`: [LogStreamDTO](../schemas/log-stream-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/log-streams/{id}"
```
