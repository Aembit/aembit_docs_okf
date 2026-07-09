---
type: reference
title: "Edge API - /edge/v1/auth"
description: "Overview of Aembit Edge API auth endpoint and its structure"
resource: https://docs.aembit.io/api-guide/edge/endpoints/auth/
interface: api
tags: [endpoint, edge]
timestamp: 2026-06-18T13:15:52-04:00
type_inferred: true
---

# Edge API - /edge/v1/auth

The authentication endpoint is the entry point for Client Workloads to establish trust with Aembit Edge. It validates Client Workload identity, with many attestation methods available, and returns an access token for subsequent API calls.

## How the `auth` endpoint works

[Section titled “How the auth endpoint works”](#how-the-auth-endpoint-works)

The Aembit Edge API `auth` endpoint provides a secure way for Client Workloads to authenticate and obtain an access token. The authentication flow consists of the following steps:

1. **Identity attestation** - Client workloads provide attestation data specific to their environment (AWS, Azure, GCP, Kubernetes, etc.)
2. **Trust validation** - Aembit Edge validates the attestation against configured Trust Providers
3. **Access Policy evaluation** - Aembit evaluates Access Policies to match the correct Client Workload and determine which credentials it can access for which Server Workloads
4. (Optional) **Resource Set validation** - If you specify a Resource Set, Aembit makes sure the Client Workload only accesses resources within the same Resource Set through the `X-Aembit-ResourceSet` header
5. **Token issuance** - Upon successful validation, Aembit Edge API returns an OAuth 2.0-style bearer token

The authentication flow supports multiple attestation methods simultaneously, allowing workloads running in hybrid or multi-cloud environments to provide multiple forms of identity proof.

No `bearerToken` in Authorization header

This authentication endpoint doesn’t pass a `bearerToken` in the `Authorization` header like most other API endpoints.

This means you **must** call the `/auth` endpoint first to get a valid access token before calling the `/credentials` endpoint. If you get any unexpected 401 errors, double-check that you aren’t setting the `Authorization` header with a `Bearer` token.

## Base structure

[Section titled “Base structure”](#base-structure)

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

[Section titled “Client Workload attestation types”](#client-workload-attestation-types)

The `client` field supports multiple attestation methods that you can use individually or in combination. Each attestation type provides different identity proof mechanisms based on where your Client Workload is running:

### AWS attestation

[Section titled “AWS attestation”](#aws-attestation)

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
                "<standard-base64-encoded-instance-identity-document>",
            "instanceIdentityDocumentSignature":
                "<standard-base64-encoded-signature>",
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

### Kubernetes attestation

[Section titled “Kubernetes attestation”](#kubernetes-attestation)

For workloads running in Kubernetes clusters, using service account tokens:

```shell
k8s: K8sDTO {} [Kubernetes workload attestation]
└─serviceAccountToken (string, nullable) [Kubernetes service account JWT token]
```

### CI/CD platform attestation

[Section titled “CI/CD platform attestation”](#cicd-platform-attestation)

For workloads running in continuous integration and deployment platforms using OpenID Connect (OIDC) identity tokens:

#### GitHub Actions attestation

[Section titled “GitHub Actions attestation”](#github-actions-attestation)

```shell
github: IdentityTokenAttestationDTO {} [GitHub Actions workflow attestation]
└─identityToken (string, nullable) [GitHub OIDC identity token for workflow verification]
```

#### Terraform Cloud attestation

[Section titled “Terraform Cloud attestation”](#terraform-cloud-attestation)

```shell
terraform: IdentityTokenAttestationDTO {} [Terraform Cloud workspace attestation]
└─identityToken (string, nullable) [Terraform Cloud OIDC identity token]
```

#### GitLab Jobs attestation

[Section titled “GitLab Jobs attestation”](#gitlab-jobs-attestation)

```shell
gitlab: IdentityTokenAttestationDTO {} [GitLab CI/CD pipeline attestation]
└─identityToken (string, nullable) [GitLab OIDC identity token for pipeline verification]
```

### Additional context

[Section titled “Additional context”](#additional-context)

```shell
sourceIP (string, nullable) [IP address of the requesting Client Workload]
```

Note

You don’t have to include the `sourceIP` in `auth` requests.
