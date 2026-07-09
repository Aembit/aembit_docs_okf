---
type: reference
title: "PATCH /api/v1/resource-sets/{id}"
description: "Patch a Resource Set"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [resource-set]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/resource-sets/{id}

Patch a Resource Set identified by its ID.

**Operation ID:** `patch-resource-set`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Resource Set

**Request Body:**

ResourceSetPatchDTO

- `application/json`: [ResourceSetPatchDTO](../schemas/resource-set-patch-dto.md)

**Responses:**

- **200**: Patched Resource Set
  - `application/json`: [ResourceSetDTO](../schemas/resource-set-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/resource-sets/{id}"
```
