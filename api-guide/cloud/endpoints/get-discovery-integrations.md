---
type: reference
title: "GET /api/v1/discovery-integrations"
description: "Get a page of Integrations"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [discovery-integration]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/discovery-integrations

Get a page of Integrations.

**Operation ID:** `get-discovery-integrations`

**Parameters:**

- **page** (query) *(optional)*: integer (int32)
- **per-page** (query) *(optional)*: integer (int32)
- **filter** (query) *(optional)*: string
- **order** (query) *(optional)*: string

**Responses:**

- **200**: Page of Integrations
  - `application/json`: [DiscoveryIntegrationListDTO](../schemas/discovery-integration-list-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/discovery-integrations?page=example-value&per-page=example-value&filter=example-value&order=example-value"
```
