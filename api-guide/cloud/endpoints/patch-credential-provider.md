---
type: reference
title: "PATCH /api/v1/credential-providers/{id}"
description: "Patch a Credential Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [credential-provider-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# PATCH /api/v1/credential-providers/{id}

Patch a Credential Provider.

**Operation ID:** `patch-credential-provider`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CredentialProviderPatchDTO

- `application/json`: [CredentialProviderPatchDTO](../schemas/credential-provider-patch-dto.md)

**Responses:**

- **201**: Patched Credential Provider
  - `application/json`: [CredentialProviderUIDTO](../schemas/credential-provider-uidto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/credential-providers/{id}"
```
