---
type: reference
title: "GET /api/v1/sso-idps"
description: "Get a page of SSO Identity Providers"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/sso-idps

Get a page of SSO Identity Providers.

**Operation ID:** `get-identity-providers`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string
- **group-by** (query) *(optional)*: string

**Responses:**

- **200**: Page of SSO Identity Providers
  - `application/json`: [SSOIdentityProviderDTOListDTO](../schemas/sso-identity-provider-dto-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps?page=example-value&per-page=example-value&filter=example-value&order=example-value&group-by=example-value"
```
