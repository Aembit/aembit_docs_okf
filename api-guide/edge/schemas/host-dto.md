---
type: reference
title: "HostDTO"
description: "Host system information for Client Workload attestation"
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
---

# HostDTO

Host system information for Client Workload attestation

**Type:** object

**Properties:**

- **hostname** *(optional)*: null,string - Client Workload hostname
- **domainName** *(optional)*: null,string - Domain name of the Client Workload host
- **process** *(optional)*: [ProcessDTO](process-dto.md) - Process information for Client Workload identification
- **sensors** *(optional)*: [SensorsDTO](sensors-dto.md) - Security sensor data for enhanced Client Workload attestation
- **systemSerialNumber** *(optional)*: null,string - Hardware serial number of the Client Workload system
- **networkInterfaces** *(optional)*: null,array
