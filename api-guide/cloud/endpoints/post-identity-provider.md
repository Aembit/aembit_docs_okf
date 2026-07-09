---
type: reference
title: "POST /api/v1/sso-idps"
description: "Create a SSO Identity Provider"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [sso-identity-provider]
timestamp: 2025-08-28T08:38:37-04:00
---

# POST /api/v1/sso-idps

Create a SSO Identity Provider.

**Operation ID:** `post-identity-provider`

**Request Body:**

SSOIdentityProviderDTO

- `application/json`: [SSOIdentityProviderDTO](../schemas/sso-identity-provider-dto.md)

**Responses:**

- **201**: Created SSO Identity Provider
  - `application/json`: [SSOIdentityProviderDTO](../schemas/sso-identity-provider-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/sso-idps"
```
