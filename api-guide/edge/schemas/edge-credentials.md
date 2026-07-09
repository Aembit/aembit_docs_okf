---
type: reference
title: "EdgeCredentials"
description: "    Credential data returned to Client Workloads based on your configured Credential Providers"
resource: https://docs.aembit.io/api-guide/edge/api-reference-edge/
interface: api
timestamp: 2026-05-07T11:06:15-07:00
---

# EdgeCredentials

    Credential data returned to Client Workloads based on your configured Credential Providers
    For AWS (AwsStsFederation), look in the aws* fields.
    For API Key and Username/Password, look in their respective fields.
    For all other types (GCP, OAuth, OIDC, Aembit), the result is in the 'token' field.

**Type:** object

**Properties:**

- **apiKey** *(optional)*: null,string - API key credential for authenticating to target services
- **token** *(optional)*: null,string - Bearer token credential for authenticating to target services/
This field contains the result for: 
GoogleWorkloadIdentityFederation (GCP WIF Token), GitLab, GitHub,
and generic JWT/OIDC credentials.
- **username** *(optional)*: null,string - Username for basic authentication credentials
- **password** *(optional)*: null,string - Password for basic authentication credentials
- **awsAccessKeyId** *(optional)*: null,string - AWS access key ID for programmatic access
- **awsSecretAccessKey** *(optional)*: null,string - AWS secret access key for programmatic access
- **awsSessionToken** *(optional)*: null,string - AWS session token for temporary credentials
