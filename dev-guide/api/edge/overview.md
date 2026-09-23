---
type: reference
title: "Get started with Aembit Edge API"
description: "Learn how to integrate Aembit Edge API into your cloud-native applications for secure credential retrieval"
resource: https://docs.aembit.io/dev-guide/api/edge/
interface: api
tags: ["edge", "api"]
timestamp: 2026-09-22T16:15:36-07:00
---

# Get started with Aembit Edge API

The Aembit Edge API enables your cloud-native applications to retrieve credentials dynamically without deploying additional infrastructure. Perfect for serverless functions, containers, and CI/CD pipelines that need secure access to third-party services.

![Aembit Edge](https://docs.aembit.io/aembit-icons/aembit-edge.svg)

[Download the Edge OpenAPI spec (YAML)](https://docs.aembit.io/edge.yaml)Direct download of the raw OpenAPI specification.

→

## What you can do with Edge API

* **Retrieve credentials on-demand** for any configured service from your CI/CD pipelines.
* **Authenticate workloads** using platform-native identity tokens (GitHub Actions, GitLab CI, AWS Lambda, etc.).
* **Remove hardcoded secrets** by fetching credentials just-in-time.
* **Support multiple credential types** including API keys, username/password, and cloud provider tokens.

## Understanding the authentication flow

Aembit Edge API uses a two-step authentication flow:

1. **Authenticate** your workload using the `/auth` endpoint to get an access token Your application proves its identity using platform-native tokens
2. **Use** that access token to call the `/credentials` endpoint Aembit provides credentials based on your configured policies

### Supported workload types

The Edge API supports identity attestation from:

* **AWS EC2**: Uses the instance identity document from the Instance Metadata Service (IMDS) through the [AWS Metadata Service Trust Provider](../../../user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider.md), or a signed AWS Security Token Service (STS) `GetCallerIdentity` request from the instance’s attached IAM role through the [AWS Role Trust Provider](../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)
* **AWS Lambda**: Uses a signed AWS STS `GetCallerIdentity` request from the function’s execution role through the AWS Role Trust Provider
* **AWS ECS**: Uses a signed AWS STS `GetCallerIdentity` request from the task’s IAM role through the AWS Role Trust Provider
* **Azure VMs**: Uses the signed attested data document from the Azure Instance Metadata Service (IMDS)
* **Google Cloud**: Uses an identity token that the GCP metadata server mints for the workload’s service account
* **Kubernetes**: Uses a service account token
* **GitHub Actions**: Uses a GitHub-issued OIDC identity token
* **GitLab Jobs**: Uses a GitLab-issued OIDC identity token
* **Terraform Cloud**: Uses a Terraform Cloud OIDC identity token
* **Any OIDC provider**: Uses an ID token from any OpenID Connect provider, for platforms with no dedicated method of their own

For the request each method sends, see [Edge API authentication methods](auth/overview.md).

### Token expiration and caching

* **Access tokens** expire in 1 hour by default
* **Credentials** have expiration times set by your Credential Provider
* **Best practice**: Cache tokens until near expiration, then refresh

## Visual tree diagram

The following is a visual representation of the Edge API endpoints and their structure in a tree format. This provides you with an at-a-glance view to understand the its organization and the relationships between different endpoints.

```plaintext
Aembit Edge API {}
├─/edge/v1/auth (POST) {}
│  ├─Headers {}
│  │  └─X-Aembit-ResourceSet (string, uuid)
│  ├─Request: AuthRequest {}
│  │  ├─clientId (string, nullable)
│  │  └─client: ClientWorkloadDetails {}
│  │     ├─sourceIP (string, nullable)
│  │     ├─aws: AwsDTO {}
│  │     │  ├─instanceIdentityDocument (string, nullable)
│  │     │  ├─instanceIdentityDocumentSignature (string, nullable)
│  │     │  ├─lambda: LambdaDTO {}
│  │     │  │  └─arn (string, nullable)
│  │     │  ├─ecs: AwsEcsDTO {}
│  │     │  │  ├─containerMetadata (string, nullable)
│  │     │  │  └─taskMetadata (string, nullable)
│  │     │  └─stsGetCallerIdentity: StsGetCallerIdentityDTO {}
│  │     │     ├─headers {}
│  │     │     │  └─[key] (string, nullable)
│  │     │     └─region (string, nullable)
│  │     ├─azure: AzureAttestationDTO {}
│  │     │  └─attestedDocument: AzureAttestedDocumentDTO {}
│  │     │     ├─encoding (string, nullable)
│  │     │     ├─signature (string, nullable)
│  │     │     └─nonce (string, nullable)
│  │     ├─gcp: GcpAttestationDTO {}
│  │     │  ├─identityToken (string, nullable)
│  │     │  └─instanceDocument (string, nullable)
│  │     ├─os: OsDTO {}
│  │     │  └─environment: EnvironmentDTO {}
│  │     │     ├─K8S_POD_NAME (string, nullable)
│  │     │     ├─CLIENT_WORKLOAD_ID (string, nullable)
│  │     │     ├─KUBERNETES_PROVIDER_ID (string, nullable)
│  │     │     └─AEMBIT_RESOURCE_SET_ID (string, nullable)
│  │     ├─k8s: K8sDTO {}
│  │     │  └─serviceAccountToken (string, nullable)
│  │     ├─host: HostDTO {}
│  │     │  ├─hostname (string, nullable)
│  │     │  ├─domainName (string, nullable)
│  │     │  ├─process: ProcessDTO {}
│  │     │  │  ├─name (string, nullable)
│  │     │  │  ├─pid (number)
│  │     │  │  ├─userId (number)
│  │     │  │  ├─userName (string, nullable)
│  │     │  │  └─exePath (string, nullable)
│  │     │  ├─sensors: SensorsDTO {}
│  │     │  │  └─crowdStrike: CrowdStrikeDTO {}
│  │     │  │     └─agentId (string, nullable)
│  │     │  └─systemSerialNumber (string, nullable)
│  │     ├─github: IdentityTokenAttestationDTO {}
│  │     │  └─identityToken (string, nullable)
│  │     ├─terraform: IdentityTokenAttestationDTO {}
│  │     │  └─identityToken (string, nullable)
│  │     └─gitlab: IdentityTokenAttestationDTO {}
│  │        └─identityToken (string, nullable)
│  └─Response: TokenDTO {}
│     ├─accessToken (string, nullable)
│     ├─tokenType (string, nullable)
│     └─expiresIn (number)
└─/edge/v1/credentials (POST) {}
   ├─Headers {}
   │  └─X-Aembit-ResourceSet (string, uuid)
   ├─Request: ApiCredentialsRequest {}
   │  ├─client: ClientWorkloadDetails {}
   │  │  ├─sourceIP (string, nullable)
   │  │  ├─aws: AwsDTO {}
   │  │  │  ├─instanceIdentityDocument (string, nullable)
   │  │  │  ├─instanceIdentityDocumentSignature (string, nullable)
   │  │  │  ├─lambda: LambdaDTO {}
   │  │  │  │  └─arn (string, nullable)
   │  │  │  ├─ecs: AwsEcsDTO {}
   │  │  │  │  ├─containerMetadata (string, nullable)
   │  │  │  │  └─taskMetadata (string, nullable)
   │  │  │  └─stsGetCallerIdentity: StsGetCallerIdentityDTO {}
   │  │  │     ├─headers {}
   │  │  │     │  └─[key] (string, nullable)
   │  │  │     └─region (string, nullable)
   │  │  ├─azure: AzureAttestationDTO {}
   │  │  │  └─attestedDocument: AzureAttestedDocumentDTO {}
   │  │  │     ├─encoding (string, nullable)
   │  │  │     ├─signature (string, nullable)
   │  │  │     └─nonce (string, nullable)
   │  │  ├─gcp: GcpAttestationDTO {}
   │  │  │  ├─identityToken (string, nullable)
   │  │  │  └─instanceDocument (string, nullable)
   │  │  ├─os: OsDTO {}
   │  │  │  └─environment: EnvironmentDTO {}
   │  │  │     ├─K8S_POD_NAME (string, nullable)
   │  │  │     ├─CLIENT_WORKLOAD_ID (string, nullable)
   │  │  │     ├─KUBERNETES_PROVIDER_ID (string, nullable)
   │  │  │     └─AEMBIT_RESOURCE_SET_ID (string, nullable)
   │  │  ├─k8s: K8sDTO {}
   │  │  │  └─serviceAccountToken (string, nullable)
   │  │  ├─host: HostDTO {}
   │  │  │  ├─hostname (string, nullable)
   │  │  │  ├─domainName (string, nullable)
   │  │  │  ├─process: ProcessDTO {}
   │  │  │  │  ├─name (string, nullable)
   │  │  │  │  ├─pid (number)
   │  │  │  │  ├─userId (number)
   │  │  │  │  ├─userName (string, nullable)
   │  │  │  │  └─exePath (string, nullable)
   │  │  │  ├─sensors: SensorsDTO {}
   │  │  │  │  └─crowdStrike: CrowdStrikeDTO {}
   │  │  │  │     └─agentId (string, nullable)
   │  │  │  └─systemSerialNumber (string, nullable)
   │  │  ├─github: IdentityTokenAttestationDTO {}
   │  │  │  └─identityToken (string, nullable)
   │  │  ├─terraform: IdentityTokenAttestationDTO {}
   │  │  │  └─identityToken (string, nullable)
   │  │  └─gitlab: IdentityTokenAttestationDTO {}
   │  │     └─identityToken (string, nullable)
   │  ├─server: ServerWorkloadDetails {}
   │  │  ├─transportProtocol (enum: TCP)
   │  │  ├─host (string, nullable)
   │  │  └─port (number)
   │  └─credentialType (enum)
   │     └─[Unknown|ApiKey|UsernamePassword|GoogleWorkloadIdentityFederation|OAuthToken|AwsStsFederation]
   └─Response: ApiCredentialsResponse {}
      ├─credentialType (enum)
      │  └─[Unknown|ApiKey|UsernamePassword|GoogleWorkloadIdentityFederation|OAuthToken|AwsStsFederation]
      ├─expiresAt (string, date-time, nullable)
      └─data: EdgeCredentials {}
         ├─apiKey (string, nullable)
         ├─token (string, nullable)
         ├─username (string, nullable)
         ├─password (string, nullable)
         ├─awsAccessKeyId (string, nullable)
         ├─awsSecretAccessKey (string, nullable)
         └─awsSessionToken (string, nullable)
```
