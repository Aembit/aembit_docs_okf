---
type: reference
title: "POST /api/v1/routings"
description: "Create a Routing"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["routing"]
timestamp: 2026-07-28T14:42:14-04:00
---

# POST /api/v1/routings

Create a Routing.

**Operation ID:** `post-routing`

**Request Body:**

RoutingDTO

- `application/json`: [RoutingDTO](../schemas/routing-dto.md)

**Responses:**

- **201**: Created Routing
  - `application/json`: [RoutingDTO](../schemas/routing-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/routings"
```
