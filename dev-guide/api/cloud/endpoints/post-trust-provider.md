---
type: reference
title: "POST /api/v1/trust-providers"
description: "Create a Trust Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider"]
timestamp: 2026-07-28T14:42:14-04:00
---

# POST /api/v1/trust-providers

Create a Trust Provider.

**Operation ID:** `post-trust-provider`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

TrustProviderDTO

- `application/json`: [TrustProviderDTO](../schemas/trust-provider-dto.md)

**Responses:**

- **200**: Created Trust Provider
  - `application/json`: [TrustProviderDTO](../schemas/trust-provider-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/trust-providers"
```
