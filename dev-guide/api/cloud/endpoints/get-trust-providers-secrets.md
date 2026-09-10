---
type: reference
title: "GET /api/v1/trust-providers/{tpId}/secrets"
description: "Get a page of Trust Provider Secrets"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider-secret"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/trust-providers/{tpId}/secrets

Get a page of Trust Provider Secrets.

**Operation ID:** `get-trust-providers-secrets`

**Parameters:**

- **tpId** (path) *(required)*: string (uuid) - ID of Trust Provider
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string

**Responses:**

- **200**: Page of Trust Providers Secrets
  - `application/json`: [TrustProviderSecretDTOListDTO](../schemas/trust-provider-secret-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/trust-providers/{tpId}/secrets?page=example-value&per-page=example-value&filter=example-value&order=example-value"
```
