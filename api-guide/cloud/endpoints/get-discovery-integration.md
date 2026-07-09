---
type: reference
title: "GET /api/v1/discovery-integrations/{id}"
description: "Get an Integration"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [discovery-integration]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/discovery-integrations/{id}

Get an Integration.

**Operation ID:** `get-discovery-integration`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Integration

**Responses:**

- **200**: Integration
  - `application/json`: [DiscoveryIntegrationDTO](../schemas/discovery-integration-dto.md)
- **204**: Integration Not Found
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/discovery-integrations/{id}"
```
