---
type: reference
title: "ServerWorkloadDetails"
description: "Target resource details for which the credential is being requested. These fields are used to match the request against your configured Access Policies."
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-09T13:57:06-07:00
---

# ServerWorkloadDetails

Target resource details for which the credential is being requested. These fields are used to match the request against your configured Access Policies.

**Type:** object

**Properties:**

- **transportProtocol** *(optional)*: [TransportProtocol](transport-protocol.md) - The protocol used to connect to the target resource. Default is TCP.
- **host** *(optional)*: null,string - The hostname, IP address, or FQDN of the target resource
- **port** *(optional)*: integer (int32) - The port number of the target resource.
