---
type: reference
title: "PATCH /api/v1/routings/{id}"
description: "Patch a Routing"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [routing]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/routings/{id}

Patch a Routing identified by its ID.

**Operation ID:** `patch-routing`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Routing

**Request Body:**

RoutingPatchDTO

- `application/json`: [RoutingPatchDTO](../schemas/routing-patch-dto.md)

**Responses:**

- **200**: Patched Routing
  - `application/json`: [RoutingDTO](../schemas/routing-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/routings/{id}"
```
