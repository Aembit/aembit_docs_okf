---
type: reference
title: "AgentControllerDTO"
description: "DTO of an individual Agent Controller for Agent Proxy management"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AgentControllerDTO

DTO of an individual Agent Controller for Agent Proxy management

**Type:** object

**Properties:**

- **id** *(optional)*: integer (int) - ID of the Agent Controller
- **externalId** *(optional)*: string (uuid) - ID of the Agent Controller
- **createdAt** *(optional)*: string (date) - Agent Controller creation Timestamp
- **version** *(optional)*: string | null - Last reported software version of the Agent Controller
- **isActive** *(optional)*: boolean (boolean) - Active status of the Agent Controller
- **name** *(required)*: string - Name of the Agent Controller
- **description** *(optional)*: string | null - Description of the Agent Controller
- **tags** *(optional)*: Array of [AgentControllerTagDTO](agent-controller-tag-dto.md) - Tags assigned to the Agent Controller
- **tlsCertificates** *(optional)*: Array of [AgentControllerTlsCertificateDTO](agent-controller-tls-certificate-dto.md) - TLS Certificates associated with the Agent Controller
- **trustProviderId** *(optional)*: string (uuid) | null - Trust Provider ID of the Agent Controller used for attested authentication
- **trustProvider** *(optional)*: [TrustProviderDTO](trust-provider-dto.md)
- **modifiedAt** *(optional)*: string (date) - Agent Controller modification Timestamp
- **isHealthy** *(optional)*: boolean (boolean) - Recently reported Agent Controller Health Status
- **lastReportedUptime** *(optional)*: integer (int64) - Last Reported Agent Controller Uptime (in seconds)
- **lastReportedHealthTime** *(optional)*: string (date) | null - Last Reported Agent Controller Health Time
- **allowedTlsHostname** *(optional)*: string | null - Allowed TLS Hostname for Aembit Managed TLS
