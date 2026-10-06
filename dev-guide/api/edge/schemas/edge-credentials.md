---
type: reference
title: "EdgeCredentials"
description: "    Credential data returned to Client Workloads based on your configured Credential Providers:"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-25T10:20:14-07:00
---

# EdgeCredentials

    Credential data returned to Client Workloads based on your configured Credential Providers:
    - For AWS (AwsStsFederation), look in the aws* fields (awsAccessKeyId, awsSecretAccessKey, awsSessionToken).
    - For API Key and Username/Password, look in their respective fields (apiKey, username, password).
    - For X.509 SVID (X509Svid), the PEM-encoded client certificate chain is returned in the 'token' field.
    - For all other token-based types (GCP Workload Identity Federation, OAuth, OIDC, Aembit), the bearer token is in the 'token' field.

**Type:** object

**Properties:**

- **apiKey** *(optional)*: null,string - API key credential for authenticating to target services
- **token** *(optional)*: null,string - Credential payload for token-based and certificate-based providers:
- For X.509-SVID credentials (X509Svid), contains the complete PEM-encoded client certificate chain issued for the workload.
- For bearer token credentials, contains the token string for GoogleWorkloadIdentityFederation (GCP WIF Token), GitLab, GitHub, and generic JWT/OIDC credentials.
- **username** *(optional)*: null,string - Username for basic authentication credentials
- **password** *(optional)*: null,string - Password for basic authentication credentials
- **awsAccessKeyId** *(optional)*: null,string - AWS access key ID for programmatic access
- **awsSecretAccessKey** *(optional)*: null,string - AWS secret access key for programmatic access
- **awsSessionToken** *(optional)*: null,string - AWS session token for temporary credentials
