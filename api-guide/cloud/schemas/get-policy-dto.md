---
type: reference
title: "GetPolicyDTO"
description: "Individual Access Policy"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# GetPolicyDTO

Individual Access Policy

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
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **clientWorkload** *(optional)*: [EntityMetaDTO](entity-meta-dto.md)
- **serverWorkload** *(optional)*: [EntityMetaDTO](entity-meta-dto.md)
- **trustProviders** *(optional)*: Array of [EntityMetaDTO](entity-meta-dto.md) - Trust Providers associated with this Access Policy
- **credentialProviders** *(optional)*: Array of [EntityMetaDTO](entity-meta-dto.md) - Credential Providers associated with this Access Policy
- **accessConditions** *(optional)*: Array of [EntityMetaDTO](entity-meta-dto.md) - Access Conditions associated with this Access Policy
