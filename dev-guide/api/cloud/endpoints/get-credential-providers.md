---
type: reference
title: "GET /api/v1/credential-providers"
description: "Get a page of Credential Providers"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["credential-provider-deprecated"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/credential-providers

Get a page of Credential Providers.

**Operation ID:** `get-credential-providers`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of Credential Providers
  - `application/json`: [CredentialProviderUIDTOCredentialProviderListDTO](../schemas/credential-provider-uidto-credential-provider-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/credential-providers?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
