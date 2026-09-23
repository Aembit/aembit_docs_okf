---
type: reference
title: "PATCH /api/v1/certificate-authorities/{id}"
description: "Patch a Standalone Certificate Authority"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["standalone-certificate-authority"]
timestamp: 2026-09-22T20:22:25-07:00
---

# PATCH /api/v1/certificate-authorities/{id}

Patch a Standalone Certificate Authority identified by its ID.

**Operation ID:** `patch-standalone-certificate-authority`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Standalone Certificate Authority

**Request Body:**

StandaloneCertificatePatchDTO

- `application/json`: [StandaloneCertificatePatchDTO](../schemas/standalone-certificate-patch-dto.md)

**Responses:**

- **200**: Patched Standalone Certificate Authority
  - `application/json`: [StandaloneCertificateResponseDTO](../schemas/standalone-certificate-response-dto.md)
- **400**: Bad Request
- **401**: Not Authenticated
- **500**: Internal Server Error
  - `application/json`: [GenericResponseDTO](../schemas/generic-response-dto.md)

**Example:**

```bash
curl -X PATCH \
  -H "Authorization: Bearer your-access-token" -H "Content-Type: application/json" \
  -d '{"example": "data"}' \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities/{id}"
```
