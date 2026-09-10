---
type: reference
title: "CPGitLabManagedAccountDTO"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-07-28T14:42:14-04:00
---

# CPGitLabManagedAccountDTO

**Extends:** [CredentialProviderV2DTO](credential-provider-v2-dto.md)

**Type:** object

**Properties:**

- **type** *(required)*: string
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
- **lifetimeTimeSpanSeconds** *(optional)*: integer (int32)
- **lifetimeExpiration** *(optional)*: null,string (date-time)
- **accessPolicyCount** *(optional)*: integer (int32) - Access Policies associated with this Credential Provider
- **groupIds** *(optional)*: null,string - Comma separated list of GitLab Group Identifiers or Paths
- **projectIds** *(optional)*: null,string - Comma separated list of GitLab Project Identifiers or Paths
- **accessLevel** *(required)*: integer (int32) - Access level code to use while assigning Managed Service Account to a group or a project
- **lifetimeInSeconds** *(required)*: integer (int32) - Lifetime (in seconds) of a Personal Access Token of the Managed Service Account
- **scope** *(required)*: string - A space separated list of scopes to be specified when requesting a Personal Access Token of a Managed Service Account
- **userId** *(optional)*: integer (int32) - GitLab user ID of the Managed Service Account.
- **username** *(optional)*: null,string - GitLab username of the Managed Service Account.
- **tokenSensitiveDataId** *(optional)*: string (uuid)
- **tokenId** *(optional)*: null,string
- **tokenExpiration** *(optional)*: null,string (date-time) - Expiration timestamp of the Personal Access Token of the Managed Service Account.
- **lastOperationTimestamp** *(optional)*: null,string (date-time) - Timestamp of the latest operation performed with the Personal Access Token of the Managed Service Account.
- **status** *(optional)*: null,string - Status of the Personal Access Token of the Managed Service Account.
- **errorMessage** *(optional)*: null,string - Contains an error message related to the last unsuccessful operation using the Personal Access Token of the Managed Service Account.
- **credentialProviderIntegrationExternalId** *(optional)*: string (uuid) - ID of the Credential Provider Integration with which this Managed GitLab Account Credential Provider is associated
