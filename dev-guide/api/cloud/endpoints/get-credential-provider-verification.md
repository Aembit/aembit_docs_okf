---
type: reference
title: "GET /api/v1/credential-providers/{id}/verification"
description: "Verify the Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-deprecated"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/credential-providers/{id}/verification

Verify the Credential Provider will successfully return a credential.

**Operation ID:** `get-credential-provider-verification`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Credential Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Details on the verification of a Credential Provider
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-providers/{id}/verification"
```
