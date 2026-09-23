---
type: reference
title: "AuditLogDTO"
description: "DTO for an individual Aembit Audit Log"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AuditLogDTO

DTO for an individual Aembit Audit Log

**Type:** object

**Properties:**

- **externalId** *(optional)*: string (uuid) - ID of an Aembit Audit Log
- **resourceSetId** *(optional)*: string (uuid) - Resource Set ID of an Aembit Audit Log
- **category** *(optional)*: null,string - Category of an Aembit Audit Log (e.g. Users, AccessPolicies, Workloads, etc.)
- **actor** *(optional)*: [AuditActorDTO](audit-actor-dto.md) - Actor DTO of an Aembit Audit Log
- **activity** *(optional)*: null,string - Activity of an Aembit Audit Log
- **target** *(optional)*: null,string - Target of an Aembit Audit Log
- **client** *(optional)*: [AuditClientDTO](audit-client-dto.md) - Remote Client DTO of an Aembit Audit Log
- **outcome** *(optional)*: [AuditOutcomeDTO](audit-outcome-dto.md) - Outcome DTO of an Aembit Audit Log
- **trustProvider** *(optional)*: [EventResultDTO](event-result-dto.md) - Attestation Result DTO for an AgentController of an Aembit Audit Log
- **severity** *(optional)*: null,string - Severity of an Aembit Audit Log
- **createdAt** *(optional)*: string (date-time) - Timestamp of when this Aembit Audit Log was created
