---
type: reference
title: "POST /api/v2/integrations"
description: "Create an Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["integration-v2"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v2/integrations

Create an Integration.

**Operation ID:** `post-integration2`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Request Body:**

IntegrationV2DTO

- `application/json`: any

**Responses:**

- **201**: Created Integration
  - `application/json`: any
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v2/integrations"
```
