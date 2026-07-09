---
type: reference
title: "GET /api/v1/integrations/{id}"
description: "Get an Integration"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [integration]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/integrations/{id}

Get an Integration.

**Operation ID:** `get-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration
- **X-Aembit-ResourceSet** (header) *(optional)*: string (uuid)

**Responses:**

- **200**: Integration
  - `application/json`: [IntegrationDTO](../schemas/integration-dto.md)
- **204**: Integration Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "X-Aembit-ResourceSet: your-resource-set-id" -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/integrations/{id}"
```
