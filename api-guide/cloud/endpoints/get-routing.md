---
type: reference
title: "GET /api/v1/routings/{id}"
description: "Get a Routing"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [routing]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/routings/{id}

Get a Routing identified by its ID.

**Operation ID:** `get-routing`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Routing

**Responses:**

- **200**: Routing
  - `application/json`: [RoutingDTO](../schemas/routing-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/routings/{id}"
```
