---
type: reference
title: "GET /api/v2/access-conditions/{id}"
description: "Get the identified Access Condition"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-condition-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v2/access-conditions/{id}

Get the Access Condition identified by its ID.

**Operation ID:** `get-access-condition2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Condition
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Access Condition
  - `application/json`: any
- **204**: Access Condition Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-conditions/{id}"
```
