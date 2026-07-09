---
type: reference
title: "PUT /api/v1/access-conditions"
description: "Update a single Access Condition"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-condition]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/access-conditions

Update a specific Access Condition identified by its ID.

**Operation ID:** `put-access-condition`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

AccessConditionDTO

- `application/json`: [AccessConditionDTO](../schemas/access-condition-dto.md)

**Responses:**

- **200**: Successfully updated Access Condition
  - `application/json`: [AccessConditionDTO](../schemas/access-condition-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-conditions"
```
