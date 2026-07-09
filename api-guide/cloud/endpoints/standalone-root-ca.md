---
type: reference
title: "GET /api/v1/certificate-authorities/{id}/root-ca"
description: "Download Standalone Root CA Certificate"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
tags: [standalone-tls-decrypt]
timestamp: 2025-08-28T08:38:37-04:00
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
