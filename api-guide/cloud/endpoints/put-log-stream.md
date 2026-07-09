---
type: reference
title: "PUT /api/v1/log-streams"
description: "Update a Log Stream"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [log-stream]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/log-streams

Update a Log Stream.

**Operation ID:** `put-log-stream`

**Request Body:**

LogStreamDTO

- `application/json`: [LogStreamDTO](../schemas/log-stream-dto.md)

**Responses:**

- **200**: Updated Log Stream
  - `application/json`: [LogStreamDTO](../schemas/log-stream-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/log-streams"
```
