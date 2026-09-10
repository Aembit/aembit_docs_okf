---
type: reference
title: "LogStreamSplunkDestinationDTO"
description: "Individual Log Stream"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# LogStreamSplunkDestinationDTO

**Extends:** [LogStreamDTO](log-stream-dto.md)

Individual Log Stream

**Type:** object

**Properties:**

- **type** *(required)*: [LogStreamDestinationType](log-stream-destination-type.md) - Log Stream Destination Type
- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **id** *(optional)*: integer (int32)
- **dataType** *(required)*: string - Log Stream Data Type (e.g. AuditLogs, etc.)
- **inProgTransactionCount** *(optional)*: integer (int32) - Log Stream In Progress Transaction Count
- **completedTransactionCount** *(optional)*: integer (int32) - Log Stream Completed Transaction Count
- **erroredTransactionCount** *(optional)*: integer (int32) - Log Stream Errored Transaction Count
- **hecHostPort** *(required)*: string
- **authenticationToken** *(required)*: string
- **hecSourceName** *(required)*: string
- **tls** *(optional)*: boolean
- **tlsVerification** *(optional)*: null,string
