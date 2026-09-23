---
type: reference
title: "GET /api/v1/access-policies/{id}"
description: "Get the identified Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-deprecated"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/access-policies/{id}

Get the Access Policy identified by its ID.

**Operation ID:** `get-access-policy`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Access Policy
  - `application/json`: [PolicyExternalDTO](../schemas/policy-external-dto.md)
- **204**: Access Policy Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-policies/{id}"
```
