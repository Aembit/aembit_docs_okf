---
type: reference
title: "PUT /api/v2/access-conditions"
description: "Update a single Access Condition"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-condition-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v2/access-conditions

Update a specific Access Condition identified by its ID.

**Operation ID:** `put-access-condition2`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

AccessConditionDTO

- `application/json`: any

**Responses:**

- **200**: Successfully updated Access Condition
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/access-conditions"
```
