---
type: reference
title: "Edge API - /edge/v1/auth"
description: "Overview of Aembit Edge API auth endpoint and its structure"
resource: https://docs.aembit.io/dev-guide/api/edge/endpoints/auth/
interface: api
tags: ["endpoint", "edge", "api"]
timestamp: 2026-09-09T13:57:06-07:00
---

# Edge API - /edge/v1/auth

The authentication endpoint is the entry point for Client Workloads to establish trust with Aembit Edge. It validates Client Workload identity, with many attestation methods available, and returns an access token for subsequent API calls.

## How the `auth` endpoint works

The Aembit Edge API `auth` endpoint provides a secure way for Client Workloads to authenticate and obtain an access token. The authentication flow consists of the following steps:

1. **Identity attestation** - Client workloads provide attestation data specific to their environment (AWS, Azure, GCP, Kubernetes, etc.)
2. **Trust validation** - Aembit Edge validates the attestation against configured Trust Providers
3. **Access Policy evaluation** - Aembit evaluates Access Policies to match the correct Client Workload and determine which credentials it can access for which Server Workloads
4. (Optional) **Resource Set validation** - If you specify a Resource Set, Aembit makes sure the Client Workload only accesses resources within the same Resource Set through the `X-Aembit-ResourceSet` header
5. **Token issuance** - Upon successful validation, Aembit Edge API returns an OAuth 2.0-style bearer token

The authentication flow supports multiple attestation methods simultaneously, allowing workloads running in hybrid or multi-cloud environments to provide multiple forms of identity proof.

> **No `bearerToken` in Authorization header**
>
> This authentication endpoint doesn’t pass a `bearerToken` in the `Authorization` header like most other API endpoints.
>
> This means you **must** call the `/auth` endpoint first to get a valid access token before calling the `/credentials` endpoint. If you get any unexpected 401 errors, double-check that you aren’t setting the `Authorization` header with a `Bearer` token.

## Base structure

The following is the base structure of the authentication endpoint, including headers, request, and response formats:

```shell
/edge/v1/auth (POST) {}
├─Headers {}
│  └─X-Aembit-ResourceSet (string, uuid) [optional]
├─Request: AuthRequest {}
│  ├─clientId (string) [Edge Client SDK ID from Trust Provider]
│  └─client: ClientWorkloadDetails {} [Workload attestation data]
└─Response: TokenDTO {}
   ├─accessToken (string, nullable) [Bearer token for API calls]
   ├─tokenType (string, nullable) [Typically "Bearer"]
   └─expiresIn (number) [Token lifetime in seconds]
```

## Client Workload attestation types

The `client` field supports multiple attestation methods that you can use individually or in combination. Each attestation type provides different identity proof mechanisms based on where your Client Workload is running:

### AWS attestation

For workloads running on Amazon Web Services, including EC2 instances, Lambda functions, and ECS containers:

```shell
aws: AwsDTO {} [AWS workload attestation]
├─instanceIdentityDocument (string, nullable) [Base64-encoded EC2 instance identity document]
├─instanceIdentityDocumentSignature (string, nullable) [Base64-encoded signature for EC2 verification]
├─lambda: LambdaDTO {} [AWS Lambda specific attestation]
│  └─arn (string, nullable) [Lambda function ARN for identity verification]
├─ecs: AwsEcsDTO {} [AWS ECS container attestation]
│  ├─containerMetadata (string, nullable) [Base64-encoded JSON string containing ECS container metadata]
│  └─taskMetadata (string, nullable) [Base64-encoded JSON string containing ECS task metadata]
└─stsGetCallerIdentity: StsGetCallerIdentityDTO {} [AWS STS identity verification]
   ├─headers {} [HTTP headers for STS GetCallerIdentity request]
   │  └─[key] (string, nullable) [Header name/value pairs]
   └─region (string, nullable) [AWS region for STS GetCallerIdentity request]
```

Example of an AWS attestation request to an EC2 instance:

```json
{
    "clientId": "<edge-sdk-client-id>",
    "client": {
        "aws": {
            "instanceIdentityDocument":
                "<base64-instance-identity-document>",
            "instanceIdentityDocumentSignature":
                "<base64-signature>",
        }
    }
}
```

**200 OK response**:

```json
{
    "accessToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkpyR3JLQ0x6RVFN...",
    "tokenType": "Bearer",
    "expiresIn": 3600
}
```

### Azure attestation

For workloads running on Microsoft Azure, using the attested data document from the Azure Instance Metadata Service (IMDS):

```shell
azure: AzureAttestationDTO {} [Azure workload attestation]
└─attestedDocument: AzureAttestedDocumentDTO {} [Azure IMDS attested data document]
   ├─encoding (string, nullable) [Encoding of the IMDS document, which is always pkcs7]
   ├─signature (string, nullable) [Base64-encoded PKCS#7 container returned by Azure IMDS]
   └─nonce (string, nullable) [Nonce you passed to the IMDS attested data endpoint]
```

Aembit validates the document’s signature against pinned Microsoft and DigiCert root certificates. It then reads the VM ID, subscription ID, and image SKU from the signed document to evaluate your match rules. Send the same `nonce` you passed to the metadata service, because Aembit compares it against the nonce inside the signed document and rejects the request when the two differ. See [Edge API authentication with Azure Instance Metadata Service](../auth/azure-metadata-service.md) for how to get the document.

Example of an Azure attestation request from a VM:

```json
{
    "clientId": "<edge-sdk-client-id>",
    "client": {
        "azure": {
            "attestedDocument": {
                "encoding": "pkcs7",
                "signature": "<base64-pkcs7-signature>",
                "nonce": "<nonce>"
            }
        }
    }
}
```

### Google Cloud Platform attestation

For workloads running on Google Cloud Platform (GCP), using GCP identity tokens and instance documents:

```shell
gcp: GcpAttestationDTO {} [Google Cloud workload attestation]
├─identityToken (string, nullable) [GCP identity token for workload attestation]
└─instanceDocument (string, nullable) [Base64-encoded GCP instance identity document]
```

The GCP Identity Token Trust Provider matches on the token’s `email` claim, so `identityToken` alone is enough. See [Edge API authentication with GCP Identity Token](../auth/gcp-identity-token.md) for how to get the token.

### Kubernetes attestation

For workloads running in Kubernetes clusters, using service account tokens:

```shell
k8s: K8sDTO {} [Kubernetes workload attestation]
└─serviceAccountToken (string, nullable) [Kubernetes service account JWT token]
```

### CI/CD platform attestation

For workloads running in continuous integration and deployment platforms using OpenID Connect (OIDC) identity tokens:

#### GitHub Actions attestation

```shell
github: IdentityTokenAttestationDTO {} [GitHub Actions workflow attestation]
└─identityToken (string, nullable) [GitHub OIDC identity token for workflow verification]
```

#### Terraform Cloud attestation

```shell
terraform: IdentityTokenAttestationDTO {} [Terraform Cloud workspace attestation]
└─identityToken (string, nullable) [Terraform Cloud OIDC identity token]
```

#### GitLab Jobs attestation

```shell
gitlab: IdentityTokenAttestationDTO {} [GitLab CI/CD pipeline attestation]
└─identityToken (string, nullable) [GitLab OIDC identity token for pipeline verification]
```

### OIDC ID Token attestation

For workloads that get an ID token from any OpenID Connect provider, including platforms with no dedicated attestation method of their own:

```shell
oidc: IdentityTokenAttestationDTO {} [OIDC ID Token attestation]
└─identityToken (string, nullable) [ID token from an OIDC-compliant identity provider]
```

Aembit validates the signature against keys you supply through the Trust Provider, so this method needs setup that the cloud provider methods don’t. See [Edge API authentication with OIDC ID Token](../auth/oidc-id-token.md).

### Additional context

```shell
sourceIP (string, nullable) [IP address of the requesting Client Workload]
```

> **Note**
>
> You don’t have to include the `sourceIP` in `auth` requests.
