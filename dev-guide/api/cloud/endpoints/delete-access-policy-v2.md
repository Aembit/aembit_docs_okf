---
type: reference
title: "DELETE /api/v2/access-policies/{id}"
description: "Delete an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# DELETE /api/v2/access-policies/{id}

Delete an Access Policy.

**Operation ID:** `delete-access-policy-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy

**Responses:**

- **204**: Deleted the Access Policy
- **400**: Bad Request
- **401**: Not Authenticated
- **404**: Not Found
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X DELETE \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies/{id}"
```
