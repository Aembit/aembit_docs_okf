---
type: reference
title: "POST /api/v1/credential-providers"
description: "Create a Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-deprecated"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/credential-providers

Create a Credential Provider.

**Operation ID:** `post-credential-provider`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CredentialProviderDTO

- `application/json`: [CredentialProviderDTO](../schemas/credential-provider-dto.md)

**Responses:**

- **201**: Created Credential Provider
  - `application/json`: [CredentialProviderUIDTO](../schemas/credential-provider-uidto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/credential-providers"
```
