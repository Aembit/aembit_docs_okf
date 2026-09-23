---
type: reference
title: "PATCH /api/v2/access-policies/{id}"
description: "Patch an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["access-policy-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v2/access-policies/{id}

Patch an Access Policy.

**Operation ID:** `patch-access-policy-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Access Policy

**Request Body:**

PatchPolicyV2DTO

- `application/json`: [PatchPolicyV2DTO](../schemas/patch-policy-v2-dto.md)

**Responses:**

- **200**: Patched Access Policy
  - `application/json`: [CreatePolicyDTO](../schemas/create-policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/access-policies/{id}"
```
