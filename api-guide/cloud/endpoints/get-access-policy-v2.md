---
type: reference
title: "GET /api/v2/access-policies/{id}"
description: "Get the identified Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-policy-v2]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v2/access-policies/{id}

Get the Access Policy identified by its ID.

**Operation ID:** `get-access-policy-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy

**Responses:**

- **200**: Access Policy
  - `application/json`: [GetPolicyDTO](../schemas/get-policy-dto.md)
- **204**: Access Policy Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies/{id}"
```
