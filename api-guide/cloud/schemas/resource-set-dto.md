---
type: reference
title: "ResourceSetDTO"
description: "Individual Resource Set"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# ResourceSetDTO

Individual Resource Set

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: string | null - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md)
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: string (date-time) | null
- **createdBy** *(optional)*: string | null
- **modifiedBy** *(optional)*: string | null
- **serverWorkloadCount** *(optional)*: integer (int32) | null - Server Workloads associated with this Resource Set
- **clientWorkloadCount** *(optional)*: integer (int32) | null - Client Workloads associated with this Resource Set
- **accessPolicyCount** *(optional)*: integer (int32) | null - Access Policies associated with this Resource Set
- **trustProviderCount** *(optional)*: integer (int32) | null - Trust Providers associated with this Resource Set
- **accessConditionCount** *(optional)*: integer (int32) | null - Access Conditions associated with this Resource Set
- **credentialProviderCount** *(optional)*: integer (int32) | null - Credential Providers associated with this Resource Set
- **roles** *(optional)*: Array of string (uuid) - Roles associated with this Resource Set
- **rolesDetails** *(optional)*: Array of [RoleDTO](role-dto.md) - Details of the Roles associated with this Resource Set
- **users** *(optional)*: Array of string - Users associated with this Resource Set
- **standaloneCertificateAuthority** *(optional)*: string (uuid) | null - Standalone Certificate Authority associated with this Resource Set
