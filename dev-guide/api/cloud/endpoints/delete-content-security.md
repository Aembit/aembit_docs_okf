---
type: reference
title: "DELETE /api/v1/content-security/{id}"
description: "Delete a Content Security"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["content-security"]
timestamp: 2026-07-28T14:42:14-04:00
---

# DELETE /api/v1/content-security/{id}

Delete a Content Security identified by its ID.

**Operation ID:** `delete-content-security`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Content Security

**Responses:**

- **204**: Successfully deleted Content Security
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/content-security/{id}"
```
