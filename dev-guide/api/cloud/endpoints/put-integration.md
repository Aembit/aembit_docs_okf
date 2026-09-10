---
type: reference
title: "PUT /api/v1/integrations"
description: "Update an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["integration"]
timestamp: 2026-07-28T14:42:14-04:00
---

# PUT /api/v1/integrations

Update an Integration.

**Operation ID:** `put-integration`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

IntegrationDTO

- `application/json`: [IntegrationDTO](../schemas/integration-dto.md)

**Responses:**

- **200**: Updated Integration
  - `application/json`: [IntegrationDTO](../schemas/integration-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/integrations"
```
