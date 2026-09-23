---
type: reference
title: "CreatePolicyDTO"
description: "Create/Update Access Policy"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# CreatePolicyDTO

Create/Update Access Policy

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **credentialProviders** *(optional)*: null,array - Credential Providers associated with this Access Policy
- **trustProviders** *(optional)*: null,array - Trust Providers associated with this Access Policy
- **accessConditions** *(optional)*: null,array - Access Conditions associated with this Access Policy
- **contentSecurity** *(optional)*: null,array - Content Security associated with this Access Policy
- **clientWorkload** *(optional)*: string (uuid) - Client Workload associated with this Access Policy
- **serverWorkload** *(optional)*: string (uuid) - Server Workload associated with this Access Policy
