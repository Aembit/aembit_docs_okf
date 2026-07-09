---
type: reference
title: "GET /api/v1/certificate-authorities/{id}"
description: "Get a Standalone Certificate Authority"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [standalone-certificate-authority]
timestamp: 2025-08-28T08:38:37-04:00
---

# GET /api/v1/certificate-authorities/{id}

Get a Standalone Certificate Authority identified by its ID.

**Operation ID:** `get-standalone-certificate-authority`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Standalone Certificate Authority

**Responses:**

- **200**: Standalone Certificate Authority
  - `application/json`: [StandaloneCertificateResponseDTO](../schemas/standalone-certificate-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities/{id}"
```
