---
type: reference
title: "PUT /api/v1/compliance-settings"
description: "Updates a global compliance setting"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [compliance]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/compliance-settings

Updates a global compliance setting

**Operation ID:** `update-compliance-setting`

**Request Body:**

SettingDTO

- `application/json`: [SettingDTO](../schemas/setting-dto.md)

**Responses:**

- **200**: Successfully updated a compliance setting
  - `application/json`: [SettingDTO](../schemas/setting-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/compliance-settings"
```
