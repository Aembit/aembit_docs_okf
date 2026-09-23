---
type: reference
title: "GET /api/v1/trust-providers/{tpId}/secrets/{secretId}"
description: "Get a Trust Provider Secret"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider-secret"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/trust-providers/{tpId}/secrets/{secretId}

Get a Trust Provider Secret identified by its ID.

**Operation ID:** `get-trust-provider-secret`

**Parameters:**

- **tpId** (path) *(required)*: string (uuid) - ID of Trust Provider
- **secretId** (path) *(required)*: string (uuid) - ID of Trust Provider Secret
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Trust Provider
  - `application/json`: [TrustProviderDTO](../schemas/trust-provider-dto.md)
- **204**: Trust Provider Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/trust-providers/{tpId}/secrets/{secretId}"
```
