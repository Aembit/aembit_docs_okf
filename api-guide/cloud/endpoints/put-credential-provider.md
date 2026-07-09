---
type: reference
title: "PUT /api/v1/credential-providers"
description: "Update a Credential Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [credential-provider-deprecated]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/credential-providers

Update a Credential Provider.

**Operation ID:** `put-credential-provider`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CredentialProviderDTO

- `application/json`: [CredentialProviderDTO](../schemas/credential-provider-dto.md)

**Responses:**

- **201**: Updated Credential Provider
  - `application/json`: [CredentialProviderUIDTO](../schemas/credential-provider-uidto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/credential-providers"
```
