---
type: reference
title: "PUT /api/v1/access-policies"
description: "Update an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-deprecated"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/access-policies

Update an Access Policy.

**Operation ID:** `put-access-policy`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

PolicyDTO

- `application/json`: [PolicyDTO](../schemas/policy-dto.md)

**Responses:**

- **200**: Updated Access Policy
  - `application/json`: [PolicyDTO](../schemas/policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-policies"
```
