---
type: reference
title: "GET /api/v1/resource-sets/{id}"
description: "Get a Resource Set"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["resource-set"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/resource-sets/{id}

Get a Resource Set identified by its ID.

**Operation ID:** `get-resource-set`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Resource Set

**Responses:**

- **200**: Resource Set
  - `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/resource-sets/{id}"
```
