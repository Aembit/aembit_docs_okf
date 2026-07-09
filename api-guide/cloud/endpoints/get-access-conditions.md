---
type: reference
title: "GET /api/v1/access-conditions"
description: "Get a page of Access Conditions"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-condition]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/access-conditions

Retrieve a page of Aembit Access Conditions.

**Operation ID:** `get-access-conditions`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Access Conditions
  - `application/json`: [AccessConditionListDTO](../schemas/access-condition-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/access-conditions?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
