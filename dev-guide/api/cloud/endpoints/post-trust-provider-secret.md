---
type: reference
title: "POST /api/v1/trust-providers/{tpId}/secrets"
description: "Create a Trust Provider Secret"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider-secret"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/trust-providers/{tpId}/secrets

Create a Trust Provider Secret.

**Operation ID:** `post-trust-provider-secret`

**Parameters:**

- **tpId** (path) *(required)*: string (uuid) - ID of Trust Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

TrustProviderDTO

- `application/json`: [TrustProviderSecretSlimDTO](../schemas/trust-provider-secret-slim-dto.md)

**Responses:**

- **200**: Updated Trust Provider Secret
  - `application/json`: [TrustProviderSecretDTO](../schemas/trust-provider-secret-dto.md)
- **201**: Created Trust Provider Secret
  - `application/json`: [TrustProviderSecretDTO](../schemas/trust-provider-secret-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/trust-providers/{tpId}/secrets"
```
