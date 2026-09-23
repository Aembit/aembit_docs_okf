---
type: reference
title: "TrustProviderMatchRuleDTO"
description: "Individual Match Rule to enforce during Trust Provider attestation"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-09-22T20:22:25-07:00
---

# TrustProviderMatchRuleDTO

Individual Match Rule to enforce during Trust Provider attestation

**Type:** object

**Properties:**

- **attribute** *(required)*: string - Match Rule Attribute. Accepted values grouped by provider:
- AWSRole: AwsAccountId, AwsRoleARN, AwsAssumedRole, AwsUsername
- AWSMetadataService: AwsAccountId, AwsArchitecture, AwsAvailabilityZone, AwsBillingProducts, AwsImageId, AwsInstanceId, AwsInstanceType, AwsKernelId, AwsMarketplaceProductCodes, AwsPendingTime, AwsPrivateIp, AwsRamdiskId, AwsRegion, AwsVersion
- Kubernetes (KubernetesServiceAccount, AzureKubernetesService, AmazonElasticKubernetesService, GoogleKubernetesEngine): KubernetesIss, KubernetesIoNamespace, KubernetesIoPodName, KubernetesIoServiceAccountName, KubernetesSub
- AzureMetadataService: AzureSku, AzureSubscriptionId, AzureVmId
- Kerberos: Principal, RealmOrDomain, Realm, SourceIp
- GitHubIdentityToken: GithubActor, GithubRepository, GithubWorkflow
- TerraformIdentityToken: TerraformOrganizationId, TerraformProjectId, TerraformWorkspaceId
- GitLabIdentityToken: GitLabSubject, GitLabProjectPath, GitLabNamespacePath, GitLabRefPath
- OidcIdToken: OidcSubject, OidcAudience, OidcIssuer, OidcCustom
- Samlv2Response: SamlSubject, SamlAudience, SamlIssuer
- AWSAlbJwt: AwsAlbJwtSubject, AwsAlbJwtIssuer, AwsAlbJwtAudience, AwsAlbJwtCustom
- UserIdentityProvider: UserAccessSubject, UserAccessAudience, UserAccessIssuer, UserAccessCustom, Email
- GcpIapJwt: GcpIapJwtSubject, GcpIapJwtIssuer, GcpIapJwtAudience, GcpIapJwtCustom
- **value** *(required)*: string - Match Rule Attribute Value
- **key** *(optional)*: null,string - Match Rule Attribute Key
