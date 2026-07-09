---
type: reference
title: "GET /api/v1/compliance-settings"
description: "Gets global compliance settings"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [compliance]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/compliance-settings

Gets global compliance settings which control the rules for creating access policy and other entities

**Operation ID:** `get-compliance-settings`

**Responses:**

- **200**: A list of compliance settings
  - `application/json`: Array of [SettingDTO](../schemas/setting-dto.md)
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/compliance-settings"
```
