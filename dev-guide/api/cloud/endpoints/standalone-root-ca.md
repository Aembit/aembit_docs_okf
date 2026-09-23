---
type: reference
title: "GET /api/v1/certificate-authorities/{id}/root-ca"
description: "Download Standalone Root CA Certificate"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["standalone-tls-decrypt"]
timestamp: 2026-09-22T20:22:25-07:00
---

# GET /api/v1/certificate-authorities/{id}/root-ca

Download the Standalone Root CA Certificate. This CA Certificate can be used for TLS verification when utilizing the Aembit TLS Decrypt feature.

**Operation ID:** `standalone-root-ca`

**Parameters:**

- **id** (path) *(required)*: string (uuid) - ID of Standalone Certificate Authority

**Responses:**

- **200**: OK
  - `application/x-pem-file`: string (binary)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/certificate-authorities/{id}/root-ca"
```
