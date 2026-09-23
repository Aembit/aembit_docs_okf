---
type: reference
title: "AuthorizationEventDTO"
description: "An individual Aembit Access Authorization Event"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AuthorizationEventDTO

An individual Aembit Access Authorization Event

**Type:** object

**Properties:**

- **authorizationChain** *(optional)*: null,array
- **meta** *(optional)*: [AuthorizationEventDataMetaDTO](authorization-event-data-meta-dto.md) - Metadata for an individual Aembit Access Authorization Event
- **outcome** *(optional)*: [AuthorizationEventOutcomeDTO](authorization-event-outcome-dto.md) - Outcome information for an individual Aembit Access Authorization Event
- **clientRequest** *(optional)*: [ClientRequestDTO](client-request-dto.md) - Client Request information for an individual Aembit Access Authorization Event
- **environment** *(optional)*: [AuthorizationEventEnvironmentDataDTO](authorization-event-environment-data-dto.md)
- **clientWorkload** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md) - Client Workload information for an individual Aembit Access Authorization Event
- **clientWorkloads** *(optional)*: null,array - Client Workload information for an individual Aembit Access Authorization Event
- **serverWorkload** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md) - Server Workload information for an individual Aembit Access Authorization Event
- **serverWorkloads** *(optional)*: null,array - Server Workload information for an individual Aembit Access Authorization Event
- **accessPolicy** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md) - Access Policy information for an individual Aembit Access Authorization Event
- **accessPolicies** *(optional)*: null,array - Access Policy information for an individual Aembit Access Authorization Event
- **trustProviders** *(optional)*: null,array - Trust Provider information for an individual Aembit Access Authorization Event
- **accessConditions** *(optional)*: null,array - Access Condition information for an individual Aembit Access Authorization Event
- **contentSecurity** *(optional)*: null,array - Content Security information for an individual Aembit Access Authorization Event
- **credentialProvider** *(optional)*: [AuthorizationEventCPResultDTO](authorization-event-cp-result-dto.md) - Credential Provider information for an individual Aembit Access Authorization Event
- **user** *(optional)*: null,string - User information of the Aembit Access Authorization Event
