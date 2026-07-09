---
type: reference
title: "GET /api/v1/users/{id}"
description: "Get a User"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [user]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/users/{id}

Get a User identified by its ID.

**Operation ID:** `get-user`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of User

**Responses:**

- **201**: User
  - `application/json`: [UserDTO](../schemas/user-dto.md)
- **204**: User Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/users/{id}"
```
