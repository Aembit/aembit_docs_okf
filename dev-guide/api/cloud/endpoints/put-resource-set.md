---
type: reference
title: "PUT /api/v1/resource-sets"
description: "Update a Resource Set"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["resource-set"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/resource-sets

Update a Resource Set.

**Operation ID:** `put-resource-set`

**Request Body:**

ResourceSetDTO

- `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)

**Responses:**

- **200**: Updated Resource Set
  - `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/resource-sets"
```
