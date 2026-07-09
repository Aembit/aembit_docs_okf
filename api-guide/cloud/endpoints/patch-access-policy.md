---
type: reference
title: "PATCH /api/v1/access-policies/{id}"
description: "Patch an Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [access-policy-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/access-policies/{id}

Patch an Access Policy.

**Operation ID:** `patch-access-policy`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

PolicyPatchDTO

- `application/json`: [PolicyPatchDTO](../schemas/policy-patch-dto.md)

**Responses:**

- **200**: Patched Access Policy
  - `application/json`: [PolicyDTO](../schemas/policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/access-policies/{id}"
```
