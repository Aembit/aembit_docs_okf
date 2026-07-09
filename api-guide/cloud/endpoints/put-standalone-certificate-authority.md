---
type: reference
title: "PUT /api/v1/certificate-authorities"
description: "Update a Standalone Certificate Authority"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [standalone-certificate-authority]
timestamp: 2025-08-28T08:38:37-04:00
---

# PUT /api/v1/certificate-authorities

Update a Standalone Certificate Authority.

**Operation ID:** `put-standalone-certificate-authority`

**Request Body:**

StandaloneCertificateDTO

- `application/json`: [StandaloneCertificateRequestDTO](../schemas/standalone-certificate-request-dto.md)

**Responses:**

- **200**: Updated Standalone Certificate Authority
  - `application/json`: [StandaloneCertificateResponseDTO](../schemas/standalone-certificate-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PUT \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities"
```
