---
type: reference
title: "PatchPolicyV2DTO"
description: "Patch request for an Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# PatchPolicyV2DTO

Patch request for an Access Policy

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - New Name for the identified entity
- **description** *(optional)*: null,string - New Description for the identified entity
- **isActive** *(optional)*: null,boolean (boolean) - New Status for the identified entity
- **tags** *(optional)*: null,array - New Tags for the identified entity
- **clientWorkload** *(optional)*: string (uuid) - Client Workload associated with this Access Policy
- **serverWorkload** *(optional)*: string (uuid) - Server Workload associated with this Access Policy
- **credentialProviders** *(optional)*: null,array - Credential Providers associated with this Access Policy
- **trustProviders** *(optional)*: null,array - Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: null,array - Access Conditions associated with this Access Policy
- **contentSecurity** *(optional)*: null,array - Content Security associated with this Access Policy
