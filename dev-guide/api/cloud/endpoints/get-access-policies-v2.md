---
type: reference
title: "GET /api/v2/access-policies"
description: "Get a page of Access Policies"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v2/access-policies

Retrieve a page of Access Policies.

**Operation ID:** `get-access-policies-v2`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string - Filter returned access policies using either a string or a key:value combination
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string
- **query** (query) *(optional)*: string

**Responses:**

- **200**: Page of Access Policies
  - `application/json`: [GetPolicyDTOListDTO](../schemas/get-policy-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/access-policies?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value&query=example-value"
```
