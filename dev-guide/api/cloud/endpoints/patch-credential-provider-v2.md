---
type: reference
title: "PATCH /api/v2/credential-providers/{id}"
description: "Patch a Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PATCH /api/v2/credential-providers/{id}

Patch a Credential Provider.

**Operation ID:** `patch-credential-provider-v2`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CredentialProviderPatchDTO

- `application/json`: [CredentialProviderPatchDTO](../schemas/credential-provider-patch-dto.md)

**Responses:**

- **200**: Patched Credential Provider
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/credential-providers/{id}"
```
