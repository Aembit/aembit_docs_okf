---
type: reference
title: "POST /api/v1/sso-idps"
description: "Create a SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/sso-idps

Create a SSO Identity Provider.

**Operation ID:** `post-identity-provider`

**Request Body:**

SSOIdentityProviderDTO

- `application/json`: any

**Responses:**

- **201**: Created SSO Identity Provider
  - `application/json`: any
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
