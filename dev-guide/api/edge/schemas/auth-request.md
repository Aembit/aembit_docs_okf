---
type: reference
title: "AuthRequest"
description: "Identity and attestation information for Client Workload authentication. "
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-09T13:57:06-07:00
---

# AuthRequest

Identity and attestation information for Client Workload authentication. 
This request initiates a session with the Aembit Edge API by providing proof of 
workload identity via a configured Trust Provider.

**Type:** object

**Properties:**

- **clientId** *(required)*: string - The Aembit ARN of the Trust Provider configured to attest this workload.
Format: 'aembit:{stack}:{tenant}:identity:{type}:{uuid}'
Where to find it:
In the Aembit Admin UI, navigate to 'Trust Providers', select your provider, 
and copy the value from the 'ID' field.
- **client** *(required)*: [ClientWorkloadDetails](client-workload-details.md) - Client Workload identifiers for authentication
