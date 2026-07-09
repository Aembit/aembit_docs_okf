---
type: reference
title: "GET /api/v1/users"
description: "Get a page of Users"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [user]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/users

Get a page of Users.

**Operation ID:** `get-users`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Users
  - `application/json`: [UserListDTO](../schemas/user-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/users?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
