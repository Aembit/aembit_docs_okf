---
type: reference
title: "POST /api/v2/credential-providers"
description: "Create a Credential Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# POST /api/v2/credential-providers

Create a Credential Provider.

**Operation ID:** `post-credential-provider2`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

CredentialProviderV2DTO

- `application/json`: any

**Responses:**

- **201**: Created Credential Provider
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/credential-providers"
```
