---
type: reference
title: "RolePatchDTO"
description: "Patch request for an individual Role"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# RolePatchDTO

Patch request for an individual Role

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - New Name for the identified entity
- **description** *(optional)*: string | null - New Description for the identified entity
- **isActive** *(optional)*: boolean (boolean) | null - New Status for the identified entity
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md) - New Tags for the identified entity
