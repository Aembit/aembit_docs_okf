---
type: reference
title: "Edge API - /edge/v1/credentials"
description: "Overview of Aembit Edge API credentials endpoint and its structure"
resource: https://docs.aembit.io/dev-guide/api/edge/endpoints/credentials/
interface: api
tags: ["endpoint", "edge", "api"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Edge API - /edge/v1/credentials

The credentials endpoint provides just-in-time credential provisioning for authenticated Client Workloads.

It returns the specific credentials needed to access target Server Workloads based on your configured Access Policies and Credential Providers.

## How the `credentials` endpoint works

1. **Authentication**: Client Workloads must first authenticate via the `/auth` endpoint to obtain a bearer token
2. **Authorization**: Aembit validates the bearer token and authorizes the request
3. **Access Policy Evaluation**: Access Policies determine which credentials the Client Workload can access
4. (Optional) **Resource Set validation** - If you specify a Resource Set, Aembit makes sure the Client Workload only accesses resources within the same Resource Set through the `X-Aembit-ResourceSet` header
5. **Credential Retrieval**: Aembit fetches credentials from the configured Credential Provider
6. **Secure Delivery**: Aembit returns credentials with appropriate expiration information

The `credentials` endpoint supports multiple credential types and can integrate with many credential stores and identity providers.

## Base structure

The following is the base structure of the credentials endpoint, including headers, request, and response formats:

```shell
/edge/v1/credentials (POST) {}
├─Headers {}
│  ├─Authorization: Bearer {token} [Required - from /auth endpoint]
│  └─X-Aembit-ResourceSet (string, uuid) [optional]
├─Request: ApiCredentialsRequest {}
│  ├─client: ClientWorkloadDetails {} [Requesting workload identity]
│  ├─server: ServerWorkloadDetails {} [Target server information]
│  └─credentialType (enum) [Type of credential requested]
└─Response: ApiCredentialsResponse {}
   ├─credentialType (enum) [Type of credential returned]
   ├─expiresAt (string, date-time, nullable) [When credentials expire]
   └─data: EdgeCredentials {} [Actual credential data]
```

## Client Workload structure (requesting side)

The `client` field identifies and provides attestation for the workload requesting credentials. This uses the same comprehensive attestation structure as the authentication endpoint, allowing Client Workloads to prove their identity through multiple methods:

```shell
client: ClientWorkloadDetails {} [Requesting workload identity]
├─sourceIP (string, nullable) [IP address of requesting workload]
├─aws: AwsDTO {} [AWS attestation methods]
│  ├─instanceIdentityDocument (string, nullable) [EC2 instance identity]
│  ├─instanceIdentityDocumentSignature (string, nullable) [EC2 signature]
│  ├─lambda: LambdaDTO {} [Lambda-specific attestation]
│  │  └─arn (string, nullable) [Lambda function ARN]
│  ├─ecs: AwsEcsDTO {} [ECS container attestation]
│  │  ├─containerMetadata (string, nullable) [ECS container metadata JSON]
│  │  └─taskMetadata (string, nullable) [ECS task metadata JSON]
│  └─stsGetCallerIdentity: StsGetCallerIdentityDTO {} [STS identity proof]
│     ├─headers {} [STS request headers]
│     │  └─[key] (string, nullable)
│     └─region (string, nullable) [AWS region for STS call]
├─azure: AzureAttestationDTO {} [Azure attestation methods]
│  └─attestedDocument: AzureAttestedDocumentDTO {}
│     ├─encoding (string, nullable) [Document encoding format]
│     ├─signature (string, nullable) [Azure attestation signature]
│     └─nonce (string, nullable) [Cryptographic nonce]
├─gcp: GcpAttestationDTO {} [Google Cloud attestation]
│  ├─identityToken (string, nullable) [GCP identity token]
│  └─instanceDocument (string, nullable) [GCE instance document]
├─os: OsDTO {} [Operating system context]
│  └─environment: EnvironmentDTO {} [Environment variables]
│     ├─K8S_POD_NAME (string, nullable)
│     ├─CLIENT_WORKLOAD_ID (string, nullable)
│     ├─KUBERNETES_PROVIDER_ID (string, nullable)
│     └─AEMBIT_RESOURCE_SET_ID (string, nullable)
├─k8s: K8sDTO {} [Kubernetes attestation]
│  └─serviceAccountToken (string, nullable) [K8s service account JWT]
├─host: HostDTO {} [Host system information]
│  ├─hostname (string, nullable) [System hostname]
│  ├─domainName (string, nullable) [Domain name]
│  ├─process: ProcessDTO {} [Process information]
│  │  ├─name (string, nullable) [Process name]
│  │  ├─pid (number) [Process ID]
│  │  ├─userId (number) [User ID]
│  │  ├─userName (string, nullable) [Username]
│  │  └─exePath (string, nullable) [Executable path]
│  ├─sensors: SensorsDTO {} [Security sensors]
│  │  └─crowdStrike: CrowdStrikeDTO {} [CrowdStrike agent data]
│  │     └─agentId (string, nullable) [Agent identifier]
│  └─systemSerialNumber (string, nullable) [Hardware serial number]
├─github: IdentityTokenAttestationDTO {} [GitHub Actions attestation]
│  └─identityToken (string, nullable) [GitHub OIDC token]
├─terraform: IdentityTokenAttestationDTO {} [Terraform Cloud attestation]
│  └─identityToken (string, nullable) [Terraform OIDC token]
├─gitlab: IdentityTokenAttestationDTO {} [GitLab CI/CD attestation]
│  └─identityToken (string, nullable) [GitLab OIDC token]
└─oidc: IdentityTokenAttestationDTO {} [OIDC ID Token attestation]
   └─identityToken (string, nullable) [ID token from an OIDC-compliant identity provider]
```

## Server Workload structure (target side)

The `server` field specifies the target Server Workload that the client wants to access. This defines where you should use the credentials that Aembit returns:

```shell
server: ServerWorkloadDetails {} [Target server information]
├─transportProtocol (enum: TCP) [Network protocol for connection]
├─host (string, nullable) [Target server hostname or IP address]
└─port (number) [Target server port number]
```

## Credential type specific responses

The `credentialType` field in both the request and response specifies what type of credentials the Client Workload is requesting and what Aembit returns. The `data` field structure varies based on this type:

### API key credentials

```shell
credentialType: ApiKey
└─data: EdgeCredentials {}
   └─apiKey (string) [API key for target service authentication]
```

### Username/password credentials

```shell
credentialType: UsernamePassword
└─data: EdgeCredentials {}
   ├─username (string) [Username for basic authentication]
   └─password (string) [Password for basic authentication]
```

### OAuth token credentials

```shell
credentialType: OAuthToken
└─data: EdgeCredentials {}
   └─token (string) [Bearer token for target service]
```

> **About Access Policy mismatches**
>
> Aembit Edge API expects specific values for each Access Policy. When any part of a `credentials` request doesn’t match those values, you’ll get the following in the `200 OK` response:
>
> ```shell
> {
>     "credentialType": "Unknown",
>     "data": {}
> }
> ```
>
> This indicates that there may be a typo or other misconfiguration in the request. Review your request and make sure that all the parameters in the request match what Aembit Edge API expects.

## Key considerations

**Security** - Aembit handles all credential responses securely and doesn’t log or cache them beyond their expiration time.

**Expiration** - Always check the `expiresAt` field and refresh credentials before they expire to avoid service interruptions.

**Error Handling** - Implement proper retry logic for credential requests, as temporary failures in credential providers can occur.

**Resource Sets** - Use the `X-Aembit-ResourceSet` header to scope credential requests to specific Access Policies when your workload operates in multiple contexts.

## Typical workflow

1. **Authenticate**: Call `/edge/v1/auth` with workload attestation data
2. **Store Token**: Securely store the returned access token
3. **Request credentials**: Call `/edge/v1/credentials` with the bearer token when accessing target services
4. **Use credentials**: Use the returned credentials to authenticate with the target service
5. **Refresh**: Monitor expiration and refresh credentials as needed
