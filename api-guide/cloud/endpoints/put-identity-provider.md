---
type: reference
title: "PUT /api/v1/sso-idps"
description: "Update a SSO Identity Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [sso-identity-provider]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/sso-idps

Update a SSO Identity Provider.

**Operation ID:** `put-identity-provider`

**Request Body:**

SSOIdentityProviderDTO

- `application/json`: [SSOIdentityProviderDTO](../schemas/sso-identity-provider-dto.md)

**Responses:**

- **200**: Updated SSO Identity Provider
  - `application/json`: [SSOIdentityProviderDTO](../schemas/sso-identity-provider-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/sso-idps"
```
