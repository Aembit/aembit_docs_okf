---
type: reference
title: "AwsIamRoleCpiDTO"
description: "DTO for AWS IAM Role Credential Provider Integration"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# AwsIamRoleCpiDTO

**Extends:** [CredentialProviderIntegrationDTO](credential-provider-integration-dto.md)

DTO for AWS IAM Role Credential Provider Integration

**Type:** object

**Properties:**

- **type** *(required)*: [CredentialProviderIntegrationType](credential-provider-integration-type.md)
- **externalId** *(optional)*: string (uuid)
- **name** *(required)*: string - Name of the Entity
- **description** *(optional)*: string | null - Description of the Entity
- **isActive** *(required)*: boolean (boolean) - True/False value that determines if this entity is Active or Disabled
- **tags** *(optional)*: Array of [TagDTO](tag-dto.md)
- **createdAt** *(optional)*: string (date-time)
- **modifiedAt** *(optional)*: string (date-time) | null
- **createdBy** *(optional)*: string | null
- **modifiedBy** *(optional)*: string | null
- **resourceSet** *(required)*: string (uuid) - ID of the Resource Set in which this Access Entity exists
- **tokenExpiration** *(optional)*: string (date-time) | null
- **lastOperationTimestamp** *(optional)*: string (date-time) | null
- **status** *(optional)*: string | null
- **errorMessage** *(optional)*: string | null
- **roleArn** *(required)*: string - ARN of the AWS IAM Role to assume for access
- **lifetimeInSeconds** *(optional)*: integer (int32) - Lifetime of the access credentials in seconds, default is 3600 seconds (1 hour)
- **fetchSecretArns** *(optional)*: boolean - Indicates whether to fetch and populate the ARNs of secrets on AWS Secrets Manager Value Credential Provider UI
