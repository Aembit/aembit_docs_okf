---
type: reference
title: "AuditLogDTO"
description: "DTO for an individual Aembit Audit Log"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuditLogDTO

DTO for an individual Aembit Audit Log

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid) - ID of an Aembit Audit Log
- **resourceSetId** *(optional)*: string (uuid) - Resource Set ID of an Aembit Audit Log
- **category** *(optional)*: string | null - Category of an Aembit Audit Log (e.g. Users, AccessPolicies, Workloads, etc.)
- **actor** *(optional)*: [AuditActorDTO](audit-actor-dto.md)
- **activity** *(optional)*: string | null - Activity of an Aembit Audit Log
- **target** *(optional)*: string | null - Target of an Aembit Audit Log
- **client** *(optional)*: [AuditClientDTO](audit-client-dto.md)
- **outcome** *(optional)*: [AuditOutcomeDTO](audit-outcome-dto.md)
- **trustProvider** *(optional)*: [EventResultDTO](event-result-dto.md)
- **severity** *(optional)*: string | null - Severity of an Aembit Audit Log
- **createdAt** *(optional)*: string (date-time) - Timestamp of when this Aembit Audit Log was created
