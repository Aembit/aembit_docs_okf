---
type: reference
title: "GET /api/v1/roles"
description: "Get a page of Roles"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["role"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/roles

Get a page of Roles.

**Operation ID:** `get-roles`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Roles
  - `application/json`: [RoleListDTO](../schemas/role-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/roles?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
