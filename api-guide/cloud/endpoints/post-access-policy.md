---
type: reference
title: "POST /api/v1/access-policies"
description: "Create an Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-policy-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/access-policies

Create an Access Policy.

**Operation ID:** `post-access-policy`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

PolicyDTO

- `application/json`: [PolicyDTO](../schemas/policy-dto.md)

**Responses:**

- **200**: Created Access Policy
  - `application/json`: [PolicyDTO](../schemas/policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-policies"
```
