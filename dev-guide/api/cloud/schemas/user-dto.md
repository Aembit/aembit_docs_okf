---
type: reference
title: "UserDTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# UserDTO

**Type:** object

**Properties:**

- **email** *(required)*: string (email)
- **externalId** *(optional)*: string (uuid)
- **roles** *(optional)*: null,array
- **rolesDetails** *(optional)*: null,array
- **firstName** *(required)*: string
- **lastName** *(required)*: string
- **phoneNumber** *(optional)*: null,string
- **createdAt** *(optional)*: string (date-time)
- **isActive** *(optional)*: boolean
- **twoFactorEnabled** *(optional)*: boolean
- **isLocked** *(optional)*: boolean
- **tags** *(optional)*: null,array
- **userTokens** *(optional)*: null,array
