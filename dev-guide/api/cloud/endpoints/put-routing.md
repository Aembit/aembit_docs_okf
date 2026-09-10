---
type: reference
title: "PUT /api/v1/routings"
description: "Update a Routing"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["routing"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/routings

Update a Routing.

**Operation ID:** `put-routing`

**Request Body:**

RoutingDTO

- `application/json`: [RoutingDTO](../schemas/routing-dto.md)

**Responses:**

- **200**: Updated Routing
  - `application/json`: [RoutingDTO](../schemas/routing-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/routings"
```
