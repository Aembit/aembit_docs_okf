---
type: reference
title: "StandaloneCertificatePatchDTO"
description: "Patch Request for an Individual Standalone Certificate Authority"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# StandaloneCertificatePatchDTO

Patch Request for an Individual Standalone Certificate Authority

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - New Name for the identified entity
- **description** *(optional)*: string | null - New Description for the identified entity
- **isActive** *(optional)*: boolean (boolean) | null - New Status for the identified entity
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md) - New Tags for the identified entity
- **leafLifetime** *(optional)*: integer (int32) | null
