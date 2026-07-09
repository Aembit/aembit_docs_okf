---
type: reference
title: "PolicyPatchDTO"
description: "Patch request for an Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PolicyPatchDTO

Patch request for an Access Policy

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - New Name for the identified entity
- **description** *(optional)*: string | null - New Description for the identified entity
- **isActive** *(optional)*: boolean (boolean) | null - New Status for the identified entity
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md) - New Tags for the identified entity
- **clientWorkload** *(optional)*: string (uuid) - Client Workload associated with this Access Policy
- **serverWorkload** *(optional)*: string (uuid) - Server Workload associated with this Access Policy
- **credentialProvider** *(optional)*: string (uuid) | null - Credential Provider associated with this Access Policy
- **trustProviders** *(optional)*: Array of string (uuid) - Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: Array of string (uuid) - Access Conditions associated with this Access Policy
