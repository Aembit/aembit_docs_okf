---
type: reference
title: "POST /api/v1/resource-sets"
description: "Create a Resource Set"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [resource-set]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/resource-sets

Create a Resource Set.

**Operation ID:** `post-resource-set`

**Request Body:**

ResourceSetDTO

- `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)

**Responses:**

- **201**: Created Resource Set
  - `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/resource-sets"
```
