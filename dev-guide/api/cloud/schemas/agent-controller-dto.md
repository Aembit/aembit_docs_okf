---
type: reference
title: "AgentControllerDTO"
description: "DTO of an individual Agent Controller for Agent Proxy management"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# AgentControllerDTO

DTO of an individual Agent Controller for Agent Proxy management

**Type:** object

**Properties:**

- **id** *(optional)*: integer (int) - ID of the Agent Controller
- **externalId** *(optional)*: string (uuid) - ID of the Agent Controller
- **createdAt** *(optional)*: string (date) - Agent Controller creation Timestamp
- **version** *(optional)*: null,string - Last reported software version of the Agent Controller
- **isActive** *(optional)*: boolean (boolean) - Active status of the Agent Controller
- **name** *(required)*: string - Name of the Agent Controller
- **description** *(optional)*: null,string - Description of the Agent Controller
- **tags** *(optional)*: null,array - Tags assigned to the Agent Controller
- **tlsCertificates** *(optional)*: null,array - TLS Certificates associated with the Agent Controller
- **trustProviderId** *(optional)*: null,string (uuid) - Trust Provider ID of the Agent Controller used for attested authentication
- **trustProvider** *(optional)*: [TrustProviderDTO](trust-provider-dto.md) - Trust Provider of the Agent Controller used for attested authentication
- **modifiedAt** *(optional)*: string (date) - Agent Controller modification Timestamp
- **isHealthy** *(optional)*: boolean (boolean) - Recently reported Agent Controller Health Status
- **lastReportedUptime** *(optional)*: integer (int64) - Last Reported Agent Controller Uptime (in seconds)
- **lastReportedHealthTime** *(optional)*: null,string (date) - Last Reported Agent Controller Health Time
- **allowedTlsHostname** *(optional)*: null,string - Allowed TLS Hostname for Aembit Managed TLS
