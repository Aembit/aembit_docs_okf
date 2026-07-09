---
type: reference
title: "WorkloadServiceEndpointDTO"
description: "Service Endpoint for a Server Workload"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# WorkloadServiceEndpointDTO

Service Endpoint for a Server Workload

**Type:** object

**Properties:**

- **externalId** *(optional)*: string | null - External ID of the Service Endpoint
- **id** *(optional)*: integer (int32) - ID of the Service Endpoint
- **host** *(required)*: string - Hostname or IP Address
- **appProtocol** *(required)*: string - Application Protocol
- **transportProtocol** *(required)*: string - Transport Protocol (e.g. TCP)
- **requestedPort** *(required)*: integer (int32) - The target port as specified by the Client Workload
- **requestedTls** *(required)*: boolean - The TLS encryption configuration of the Client Workload
- **port** *(required)*: integer (int32) - The target port to which the Agent/Proxy will communicate
- **tls** *(required)*: boolean - The TLS encryption configuration which will be used by the Agent/Proxy
- **workloadServiceAuthentication** *(optional)*: [WorkloadServiceAuthenticationDTO](workload-service-authentication-dto.md)
- **tlsVerification** *(required)*: string - TLS Verification configuration for the Agent/Proxy to Server Workload connection
- **httpHeaders** *(optional)*: Array of [StringStringKeyValuePair](string-string-key-value-pair.md) - Static HTTP Headers to include for transmission to the Server Workload
