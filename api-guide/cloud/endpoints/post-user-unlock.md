---
type: reference
title: "POST /api/v1/users/{id}/unlock"
description: "Unlock a User"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [user]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/users/{id}/unlock

Unlock a User identified by its ID.

**Operation ID:** `post-user-unlock`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of User

**Responses:**

- **200**: Successfully unlocked User
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/users/{id}/unlock"
```
