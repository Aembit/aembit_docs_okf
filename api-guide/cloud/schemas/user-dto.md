---
type: reference
title: "UserDTO"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# UserDTO

**Type:** object

**Properties:**

- **email** *(required)*: string (email)
- **externalId** *(optional)*: string (uuid)
- **roles** *(optional)*: Array of string (uuid)
- **rolesDetails** *(optional)*: Array of [RoleDTO](role-dto.md)
- **firstName** *(required)*: string
- **lastName** *(required)*: string
- **phoneNumber** *(optional)*: string | null
- **createdAt** *(optional)*: string (date-time)
- **isActive** *(optional)*: boolean
- **twoFactorEnabled** *(optional)*: boolean
- **isLocked** *(optional)*: boolean
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md)
- **userTokens** *(optional)*: Array of [UserTokensDTO](user-tokens-dto.md)
