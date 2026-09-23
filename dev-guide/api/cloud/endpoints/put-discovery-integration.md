---
type: reference
title: "PUT /api/v1/discovery-integrations"
description: "Update an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["discovery-integration"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PUT /api/v1/discovery-integrations

Update an Integration.

**Operation ID:** `put-discovery-integration`

**Request Body:**

DiscoveryIntegrationDTO

- `application/json`: [DiscoveryIntegrationDTO](../schemas/discovery-integration-dto.md)

**Responses:**

- **200**: Updated Integration
  - `application/json`: [DiscoveryIntegrationDTO](../schemas/discovery-integration-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/discovery-integrations"
```
