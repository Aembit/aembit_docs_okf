---
type: reference
title: "AwsDTO"
description: "AWS-specific attestation data for Client Workload identification"
resource: https://docs.aembit.io/dev-guide/api/edge/api-reference-edge/
interface: api
timestamp: 2026-09-09T13:57:06-07:00
---

# AwsDTO

AWS-specific attestation data for Client Workload identification

**Type:** object

**Properties:**

- **instanceIdentityDocument** *(optional)*: null,string - Base64-encoded AWS instance identity document
- **instanceIdentityDocumentSignature** *(optional)*: null,string - Base64-encoded signature for AWS instance identity document verification
- **lambda** *(optional)*: [LambdaDTO](lambda-dto.md) - AWS Lambda function information for serverless workload attestation
- **ecs** *(optional)*: [AwsEcsDTO](aws-ecs-dto.md) - AWS ECS container and task metadata for workload attestation
- **stsGetCallerIdentity** *(optional)*: [StsGetCallerIdentityDTO](sts-get-caller-identity-dto.md) - AWS STS GetCallerIdentity request data for identity verification
