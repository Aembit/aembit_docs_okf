---
type: how-to
title: "Configure a Username & Password Credential Provider"
description: "How to create and use a Username & Password Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/username-password/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Configure a Username & Password Credential Provider

The Username & Password credential provider is tailored for Server Workloads requiring username and password authentication, such as databases and Server Workloads utilizing HTTP Basic authentication.

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

To configure a Username & Password Credential Provider, follow the steps outlined below.

1. Log into your Aembit Tenant.

2. Once you are logged into your tenant, click on the **Credential Providers** tab in the left sidebar. You are directed to the Credential Providers page displaying a list of existing Credential Providers. In this example, there are no existing Credential Providers.

   ![Credential Providers - Main Page Empty](https://docs.aembit.io/_astro/credential_providers_main_page_empty.BTUxwZGj_ZknLOA.webp)

3. Click on the **New** button to open the Credential Providers dialog window.

   ![Credential Providers - Dialog Window Empty](https://docs.aembit.io/_astro/credential_providers_username_password_dialog_window_empty.XcYWbRhM_2r49ii.webp)

4. In the Credential Providers dialog window, enter the following information:

   * **Name** - Name of the Credential Provider.

   * **Description** - An optional text description of the Credential Provider.

   * **Credential Type** - A dropdown menu that enables you to configure the Credential Provider type. Select **Username & Password**.

   * **Username** - The username serves as the access credential associated with the account or system, allowing authentication for accessing the Server Workload.

     Depending on the context, the **Username** could take various forms:

     * **Email Address** - Use the full email address associated with the account.
     * **Master User** - In certain systems, this might be a master user account that has privileged access.
     * **Account Username** - This could be a specific username assigned to the account for authentication purposes.

     Please make sure to review your Server Workload documentation to determine what is considered a username in that specific context.

     * **Password** - The corresponding password for the provided username. Please refer to the specific Server Workload documentation for accurate configuration details.

   ![Credential Providers - Dialog Window Completed](https://docs.aembit.io/_astro/credential_providers_username_password_dialog_window_completed.Ba2bA94Q_Z2b2JjD.webp)

5. Click **Save** when finished. You will be directed back to the Credential Providers page, where you will see your newly created Credential Provider.

   ![Credential Providers - Main Page With New Credential Provider](https://docs.aembit.io/_astro/credential_providers_username_password_main_page_with_new_credential_provider.skKCXG9m_26YsH2.webp)

## Related

**Compatible trust providers**

* [AWS Metadata Service](../trust-providers/aws-metadata-service-trust-provider.md)
* [AWS Role](../trust-providers/aws-role-trust-provider.md)
* [Azure Instance Metadata Service](../trust-providers/azure-metadata-service-trust-provider.md)
* [Certificate Signed Attestation](../trust-providers/certificate-signed-attestation-trust-provider.md)
* [GCP Identity Token](../trust-providers/gcp-identity-token-trust-provider.md)
* [GitHub Action ID Token](../trust-providers/github-trust-provider.md)
* [GitLab Job ID Token](../trust-providers/gitlab-trust-provider.md)
* [Kerberos](../trust-providers/kerberos-trust-provider.md)
* [Kubernetes Service Account](../trust-providers/kubernetes-service-account-trust-provider.md)
* [OIDC ID Token](../trust-providers/oidc-id-token-trust-provider.md)
* [SAMLv2 Response](../trust-providers/saml-response-trust-provider.md)
* [Terraform Cloud Identity Token](../trust-providers/terraform-cloud-identity-token-trust-provider.md)

**Server workloads that accept this credential**

* [AWS MySQL](../server-workloads/guides/aws-mysql.md)
* [AWS PostgreSQL](../server-workloads/guides/aws-postgres.md)
* [AWS Redshift](../server-workloads/guides/aws-redshift.md)
* [Local MySQL](../server-workloads/guides/local-mysql.md)
* [Local PostgreSQL](../server-workloads/guides/local-postgres.md)
* [Local Redis](../server-workloads/guides/local-redis.md)
* [Oracle Database](../server-workloads/guides/oracle-database.md)
* [SauceLabs](../server-workloads/guides/saucelabs.md)
