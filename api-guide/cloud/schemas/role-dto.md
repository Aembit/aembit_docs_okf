---
type: reference
title: "RoleDTO"
description: "Individual Role"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# RoleDTO

Individual Role

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
- **usersCount** *(optional)*: integer (int32) - Number of Users associated with this Role
- **credentialProvidersCount** *(optional)*: integer (int32) - Number of Credential Providers associated with this Role
- **isSystem** *(optional)*: boolean - True if this is a system included Role (e.g. SuperAdmin or Auditor)
- **permissions** *(optional)*: Array of [PermissionDTO](permission-dto.md) - Permissions assigned to this Role
- **resourceSets** *(optional)*: Array of [ResourceSetDTO](resource-set-dto.md) - Resource Sets assigned to this Role
