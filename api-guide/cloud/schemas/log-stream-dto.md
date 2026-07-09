---
type: reference
title: "LogStreamDTO"
description: "Individual Log Stream"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# LogStreamDTO

Individual Log Stream

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
- **id** *(optional)*: integer (int32)
- **dataType** *(required)*: string - Log Stream Data Type (e.g. AuditLogs, etc.)
- **type** *(required)*: [LogStreamDestinationType](log-stream-destination-type.md)
- **inProgTransactionCount** *(optional)*: integer (int32) - Log Stream In Progress Transaction Count
- **completedTransactionCount** *(optional)*: integer (int32) - Log Stream Completed Transaction Count
- **erroredTransactionCount** *(optional)*: integer (int32) - Log Stream Errored Transaction Count
