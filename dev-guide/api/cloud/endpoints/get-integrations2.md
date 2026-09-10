---
type: reference
title: "GET /api/v2/integrations"
description: "Get a page of Integrations"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["integration-v2"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v2/integrations

Get a page of Integrations.

**Operation ID:** `get-integrations2`

**Parameters:**

- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)
- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string

**Responses:**

- **200**: Page of Integrations
  - `application/json`: [IntegrationV2DTOIntegrationListDTO](../schemas/integration-v2-dto-integration-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v2/integrations?page=example-value&per-page=example-value&filter=example-value&order=example-value"
```
