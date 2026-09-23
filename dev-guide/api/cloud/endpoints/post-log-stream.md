---
type: reference
title: "POST /api/v1/log-streams"
description: "Create a Log Stream"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["log-stream"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/log-streams

Create a Log Stream.

**Operation ID:** `post-log-stream`

**Request Body:**

LogStreamDTO

- `application/json`: any

**Responses:**

- **201**: Created Log Stream
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/log-streams"
```
