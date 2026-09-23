---
type: reference
title: "PermissionDTO"
description: "Individual Permission details"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# PermissionDTO

Individual Permission details

**Type:** object

**Properties:**

- **name** *(optional)*: null,string - Name of the Permission Target
- **read** *(optional)*: boolean - True if this permission allows access to Read the Permission Target, False otherwise
- **write** *(optional)*: boolean - True if this permission allows access to Write the Permission Target, False otherwise
- **isWritable** *(optional)*: boolean - True if this permission allows access to Write the Permission Target, False otherwise
- **isReadable** *(optional)*: boolean - True if this permission allows access to Read the Permission Target, False otherwise
- **accessLevel** *(optional)*: null,string - Description of the Permission level
