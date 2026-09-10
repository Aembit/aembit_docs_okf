---
type: reference
title: "PUT /api/v1/signin-policies/sso"
description: "Update a SSO SignOn Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-sign-on-policy"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/signin-policies/sso

Update a SSO SignOn Policy.

**Operation ID:** `put-SSO-signon Policy`

**Request Body:**

SSOSignInPolicyDTO

- `application/json`: [SSOSignInPolicyDTO](../schemas/sso-sign-in-policy-dto.md)

**Responses:**

- **200**: Updated SSO SignOn Policy
  - `application/json`: [SSOSignInPolicyDTO](../schemas/sso-sign-in-policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/signin-policies/sso"
```
