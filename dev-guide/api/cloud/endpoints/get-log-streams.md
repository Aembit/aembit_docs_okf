---
type: reference
title: "GET /api/v1/log-streams"
description: "Get a page of Log Streams"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["log-stream"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/log-streams

Get a page of Log Streams.

**Operation ID:** `get-log-streams`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string

**Responses:**

- **200**: Page of Log Streams
  - `application/json`: [LogStreamListDTO](../schemas/log-stream-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/log-streams?page=example-value&per-page=example-value&filter=example-value&order=example-value"
```
