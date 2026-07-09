---
type: reference
title: "POST /api/v1/access-conditions"
description: "Create an Access Condition"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-condition]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/access-conditions

Create an Aembit Access Condition which can then be associated with an Access Policy.

**Operation ID:** `post-access-condition`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

AccessConditionDTO

- `application/json`: [AccessConditionDTO](../schemas/access-condition-dto.md)

**Responses:**

- **201**: Successfully created Access Condition
  - `application/json`: [AccessConditionDTO](../schemas/access-condition-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-conditions"
```
