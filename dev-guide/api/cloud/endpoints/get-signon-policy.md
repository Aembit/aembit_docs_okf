---
type: reference
title: "GET /api/v1/signin-policies"
description: "Get a SignOn Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sign-on-policy"]
timestamp: 2026-09-22T20:22:25-07:00
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
