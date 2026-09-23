---
type: reference
title: "ResourceSetDTO"
description: "Individual Resource Set"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# ResourceSetDTO

Individual Resource Set

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
- **serverWorkloadCount** *(optional)*: null,integer (int32) - Server Workloads associated with this Resource Set
- **clientWorkloadCount** *(optional)*: null,integer (int32) - Client Workloads associated with this Resource Set
- **accessPolicyCount** *(optional)*: null,integer (int32) - Access Policies associated with this Resource Set
- **trustProviderCount** *(optional)*: null,integer (int32) - Trust Providers associated with this Resource Set
- **accessConditionCount** *(optional)*: null,integer (int32) - Access Conditions associated with this Resource Set
- **credentialProviderCount** *(optional)*: null,integer (int32) - Credential Providers associated with this Resource Set
- **roles** *(optional)*: null,array - Roles associated with this Resource Set
- **rolesDetails** *(optional)*: null,array - Details of the Roles associated with this Resource Set
- **users** *(optional)*: null,array - Users associated with this Resource Set
- **standaloneCertificateAuthority** *(optional)*: null,string (uuid) - Standalone Certificate Authority associated with this Resource Set
