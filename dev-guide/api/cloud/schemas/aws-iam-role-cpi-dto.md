---
type: reference
title: "AwsIamRoleCpiDTO"
description: "DTO for AWS IAM Role Credential Provider Integration"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# AwsIamRoleCpiDTO

**Extends:** [CredentialProviderIntegrationDTO](credential-provider-integration-dto.md)

DTO for AWS IAM Role Credential Provider Integration

**Type:** object

**Properties:**

- **type** *(required)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: null,string - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: null,array
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: null,string (date-time)
- **createdBy** *(optional)*: null,string
- **modifiedBy** *(optional)*: null,string
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **tokenExpiration** *(optional)*: null,string (date-time)
- **lastOperationTimestamp** *(optional)*: null,string (date-time)
- **status** *(optional)*: null,string
- **errorMessage** *(optional)*: null,string
- **roleArn** *(required)*: string - ARN of the AWS IAM Role to assume for access
- **lifetimeInSeconds** *(optional)*: integer (int32) - Lifetime of the access credentials in seconds, default is 3600 seconds (1 hour)
- **fetchSecretArns** *(optional)*: boolean - Indicates whether to fetch and populate the ARNs of secrets on AWS Secrets Manager Value Credential Provider UI
