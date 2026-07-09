---
type: reference
title: "PermissionDTO"
description: "Individual Permission details"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# PermissionDTO

Individual Permission details

**Type:** object

**Properties:**

- **name** *(optional)*: string | null - Name of the Permission Target
- **read** *(optional)*: boolean - True if this permission allows access to Read the Permission Target, False otherwise
- **write** *(optional)*: boolean - True if this permission allows access to Write the Permission Target, False otherwise
- **isWritable** *(optional)*: boolean - True if this permission allows access to Write the Permission Target, False otherwise
- **isReadable** *(optional)*: boolean - True if this permission allows access to Read the Permission Target, False otherwise
- **accessLevel** *(optional)*: string | null - Description of the Permission level
