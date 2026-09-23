---
type: reference
title: "GET /api/v1/routings"
description: "Get a page of Routings"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["routing"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/routings

Get a page of Routings.

**Operation ID:** `get-routings`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Routings
  - `application/json`: [RoutingDTOListDTO](../schemas/routing-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/routings?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
