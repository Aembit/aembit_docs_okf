---
type: reference
title: "GET /api/v1/sso-idps/{id}/userinfo-metrics"
description: "Get rolling 24-hour UserInfo health telemetry for an SSO Identity Provider"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["sso-identity-provider"]
timestamp: 2026-11-03T18:06:18-08:00
---

# GET /api/v1/sso-idps/{id}/userinfo-metrics

Get rolling 24-hour UserInfo health telemetry for an SSO Identity Provider

**Operation ID:** `get-sso-idp-userinfo-metrics`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of SSO Identity Provider

**Responses:**

- **200**: UserInfo 24-hour metrics summary
  - `application/json`: [IdpUserInfoMetricsDTO](../schemas/idp-user-info-metrics-dto.md)
- **404**: SSO Identity Provider not found
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/sso-idps/{id}/userinfo-metrics"
```
