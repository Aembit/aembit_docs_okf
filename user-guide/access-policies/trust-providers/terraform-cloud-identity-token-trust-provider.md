---
type: reference
title: "Terraform Cloud Identity Token Trust Provider"
description: "This page describes the steps required to configure the Terraform Cloud Identity Token Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/terraform-cloud-identity-token-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Terraform Cloud Identity Token Trust Provider


The Terraform Cloud Identity Token Trust Provider verifies the identities of Client Workloads within Terraform Cloud using identity tokens. These tokens include metadata such as organization, project, and workspace details, ensuring secure and authenticated access to resources.

## Match rules

The following match rules are available for this Trust Provider type:

| Data                        | Description                                                                           | Example             |
| --------------------------- | ------------------------------------------------------------------------------------- | ------------------- |
| terraform\_organization\_id | The Terraform organization that is executing the run.                                 | org-abcdefghijklmno |
| terraform\_project\_id      | The specific project within the Terraform organization that is running the operation. | prj-abcdefghijklmno |
| terraform\_workspace\_id    | The ID associated with the Terraform workspace where the run is being conducted.      | ws-abcdefghijklmno  |

For additional information about Terraform Cloud Identity Token, please refer to [Terraform Workload Identity](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/dynamic-provider-credentials/workload-identity-tokens).

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Terraform Cloud ID Token Organization ID](../client-workloads/identification/terraform-cloud-id-token-organization-id.md)
* [Terraform Cloud ID Token Project ID](../client-workloads/identification/terraform-cloud-id-token-project-id.md)
* [Terraform Cloud ID Token Workspace ID](../client-workloads/identification/terraform-cloud-id-token-workspace-id.md)
