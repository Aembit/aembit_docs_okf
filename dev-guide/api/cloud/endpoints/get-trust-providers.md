---
type: reference
title: "GET /api/v1/trust-providers"
description: "Get a page of Trust Providers"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["trust-provider"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/trust-providers

Get a page of Trust Providers.

**Operation ID:** `get-trust-providers`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string
- **active** (query) *(optional)*: boolean

**Responses:**

- **200**: Page of Trust Providers
  - `application/json`: [TrustProviderListDTO](../schemas/trust-provider-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/trust-providers?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value&active=example-value"
```
