---
type: reference
title: "GET /api/v1/signin-policies"
description: "Get a SignOn Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [sign-on-policy]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/signin-policies

Get a SignOn Policy by its name.

**Operation ID:** `get-signon-policy`

**Responses:**

- **200**: SignOn Policy
  - `application/json`: [GetSignInPolicyDTO](../schemas/get-sign-in-policy-dto.md)
- **204**: SignOn Policy Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/signin-policies"
```
