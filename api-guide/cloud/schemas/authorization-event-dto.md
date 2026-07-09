---
type: reference
title: "AuthorizationEventDTO"
description: "An individual Aembit Access Authorization Event"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AuthorizationEventDTO

An individual Aembit Access Authorization Event

**Type:** object

**Properties:**

- **meta** *(optional)*: [AuthorizationEventDataMetaDTO](authorization-event-data-meta-dto.md)
- **outcome** *(optional)*: [AuthorizationEventOutcomeDTO](authorization-event-outcome-dto.md)
- **clientRequest** *(optional)*: [ClientRequestDTO](client-request-dto.md)
- **environment** *(optional)*: [AuthorizationEventEnvironmentDataDTO](authorization-event-environment-data-dto.md)
- **clientWorkload** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md)
- **serverWorkload** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md)
- **accessPolicy** *(optional)*: [AuthorizationEventEntityResultDTO](authorization-event-entity-result-dto.md)
- **trustProviders** *(optional)*: Array of [AuthorizationEventAtttestationResultDTO](authorization-event-atttestation-result-dto.md) - Trust Provider information for an individual Aembit Access Authorization Event
- **accessConditions** *(optional)*: Array of [AuthorizationEventAtttestationResultDTO](authorization-event-atttestation-result-dto.md) - Access Condition information for an individual Aembit Access Authorization Event
- **credentialProvider** *(optional)*: [AuthorizationEventCPResultDTO](authorization-event-cp-result-dto.md)
