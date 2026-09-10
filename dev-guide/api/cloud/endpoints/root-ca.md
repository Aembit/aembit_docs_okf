---
type: reference
title: "GET /api/v1/root-ca"
description: "Download Tenant Root CA Certificate"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
tags: ["tls-decrypt"]
timestamp: 2026-07-28T14:42:14-04:00
---

# GET /api/v1/root-ca

Download the Tenant Root CA Certificate. This CA Certificate can be used for TLS verification when utilizing the Aembit TLS Decrypt feature.

**Operation ID:** `root-ca`

**Responses:**

- **200**: OK
  - `application/x-pem-file`: string (binary)

**Example:**

```bash
curl -X GET \
  -H "Authorization: Bearer your-access-token" \
  "https://your-tenant.aembit.io/api/v1/root-ca"
```
