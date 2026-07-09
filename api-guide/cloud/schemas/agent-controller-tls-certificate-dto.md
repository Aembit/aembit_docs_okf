---
type: reference
title: "AgentControllerTlsCertificateDTO"
description: "Agent Controller TLS Certificate information"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AgentControllerTlsCertificateDTO

Agent Controller TLS Certificate information

**Type:** object

**Properties:**

- **subject** *(required)*: string - Subject of the Certificate
- **serialNumber** *(required)*: string - Serial Number of the Certificate
- **thumbprint** *(required)*: string - Thumbprint of the Certificate
- **notBefore** *(required)*: string (date-time) - Creation Timestamp of the Certificate
- **notAfter** *(required)*: string (date-time) - Expiration Timestamp of the Certificate
- **hostName** *(required)*: string - Last reported Hostname for the Agent Controller
- **createdAt** *(required)*: string (date-time) - Creation Timestamp for this Agent Controller TLS Certificate
- **isManagedByAembit** *(optional)*: boolean (boolean) - True if the Agent Controller TLS Certificate is managed by Aembit
