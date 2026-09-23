---
type: reference
title: "POST /api/v1/certificate-authorities"
description: "Create a Standalone Certificate Authority"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["standalone-certificate-authority"]
timestamp: 2026-09-22T20:22:25-07:00
---

# POST /api/v1/certificate-authorities

Create a Standalone Certificate Authority.

**Operation ID:** `post-standalone-certificate-authority`

**Request Body:**

StandaloneCertificateDTO

- `application/json`: [StandaloneCertificateRequestDTO](../schemas/standalone-certificate-request-dto.md)

**Responses:**

- **201**: Created Standalone Certificate Authority
  - `application/json`: [StandaloneCertificateResponseDTO](../schemas/standalone-certificate-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X POST \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities"
```
