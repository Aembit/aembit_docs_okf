---
type: reference
title: "PolicyPatchDTO"
description: "Patch request for an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# PolicyPatchDTO

Patch request for an Access Policy

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **clientWorkload** *(optional)*: string (uuid) - Client Workload associated with this Access Policy
- **serverWorkload** *(optional)*: string (uuid) - Server Workload associated with this Access Policy
- **credentialProvider** *(optional)*: null,string (uuid) - Credential Provider associated with this Access Policy
- **trustProviders** *(optional)*: null,array - Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: null,array - Access Conditions associated with this Access Policy
- **contentSecurity** *(optional)*: null,array - Content Security associated with this Access Policy
