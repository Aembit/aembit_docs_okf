---
type: reference
title: "PUT /api/v1/signin-policies/mfa"
description: "Update a MFA SignOn Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["mfa-sign-on-policy"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/signin-policies/mfa

Update a MFA SignOn Policy.

**Operation ID:** `put-mfa-signon Policy`

**Request Body:**

MFASignInPolicyDTO

- `application/json`: [MFASignInPolicyDTO](../schemas/mfa-sign-in-policy-dto.md)

**Responses:**

- **200**: Updated MFA SignOn Policy
  - `application/json`: [MFASignInPolicyDTO](../schemas/mfa-sign-in-policy-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **403**: Forbidden
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/signin-policies/mfa"
```
