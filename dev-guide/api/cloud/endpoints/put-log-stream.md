---
type: reference
title: "PUT /api/v1/log-streams"
description: "Update a Log Stream"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["log-stream"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/log-streams

Update a Log Stream.

**Operation ID:** `put-log-stream`

**Request Body:**

LogStreamDTO

- `application/json`: any

**Responses:**

- **200**: Updated Log Stream
  - `application/json`: any
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
