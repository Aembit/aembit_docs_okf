---
type: reference
title: "ServerWorkloadDetails"
description: "Target resource details for which the credential is being requested. "
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-22T20:31:55-07:00
---

# ServerWorkloadDetails

Target resource details for which the credential is being requested. 
These fields provide the server-side context used to match the request against 
your configured Aembit Access Policies.

**Type:** object

**Properties:**

- **transportProtocol** *(optional)*: [TransportProtocol](transport-protocol.md) - The protocol used to connect to the target resource. Default is TCP.
- **host** *(optional)*: null,string - The hostname, IP address, or FQDN of the target resource as defined in your Aembit Access Policy. 
For cloud credentials (e.g., AWS STS, Azure Entra, or GCP WIF / Google Cloud APIs), this should be the endpoint of the 
specific resource you are accessing, not the identity provider's endpoint.
- **port** *(optional)*: integer (int32) - The port number of the target resource as defined in your Aembit Access Policy.
