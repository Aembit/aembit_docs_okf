---
type: reference
title: "PATCH /api/v1/roles/{id}"
description: "Patch a Role"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["role"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v1/roles/{id}

Patch a Role identified by its ID.

**Operation ID:** `patch-role`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Role

**Request Body:**

RolePatchDTO

- `application/json`: [RolePatchDTO](../schemas/role-patch-dto.md)

**Responses:**

- **200**: Patch Role
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/roles/{id}"
```
