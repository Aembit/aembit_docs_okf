---
type: reference
title: "RoleDTO"
description: "Individual Role"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# RoleDTO

Individual Role

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
- **usersCount** *(optional)*: integer (int32) - Number of Users associated with this Role
- **credentialProvidersCount** *(optional)*: integer (int32) - Number of Credential Providers associated with this Role
- **isSystem** *(optional)*: boolean - True if this is a system included Role (e.g. SuperAdmin or Auditor)
- **permissions** *(optional)*: null,array - Permissions assigned to this Role
- **resourceSets** *(optional)*: null,array - Resource Sets assigned to this Role
