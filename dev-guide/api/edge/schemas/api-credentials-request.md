---
type: reference
title: "ApiCredentialsRequest"
description: "Request payload for retrieving credentials for a Client Workload"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-09T13:57:06-07:00
---

# ApiCredentialsRequest

Request payload for retrieving credentials for a Client Workload

**Type:** object

**Properties:**

- **client** *(required)*: [ClientWorkloadDetails](client-workload-details.md) - Identity and attestation details for the client workload requesting credentials.
Populate this object with the same workload identity context used for /edge/v1/auth,
including any platform-specific attestation fields required for that workload type (for
example, Kubernetes service account token, OIDC identity token, or cloud instance attestation data).
These fields are evaluated as part of access policy matching for the credentials request.
- **server** *(required)*: [ServerWorkloadDetails](server-workload-details.md) - Target resource details for which the credential is being requested.
- **credentialType** *(required)*: [CredentialProviderTypes](credential-provider-types.md) - Type of credential being requested from your configured Credential Provider
- **connectionMetadata** *(optional)*: [ConnectionMetadata](connection-metadata.md) - Filter values to be used if your access policy is configured with multiple credential providers
- **certSigningRequest** *(optional)*: null,string
