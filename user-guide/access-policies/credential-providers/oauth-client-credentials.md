---
type: how-to
title: "Configure an OAuth 2.0 Client Credentials Credential Provider"
description: "How to create and use an OAuth 2.0 Client Credentials Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/oauth-client-credentials/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# Configure an OAuth 2.0 Client Credentials Credential Provider

The OAuth 2.0 Client Credentials Flow, described in [OAuth 2.0 RFC 6749 (section 4.4)](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4), is a method in which an application can obtain an access token by using its unique credentials such as client ID and client secret.

This process is typically used when an application needs to authenticate itself, without requiring user input, to access protected resources.

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

To configure an OAuth 2.0 Client Credentials Credential Provider, follow the steps outlined below.

1. Log into your Aembit Tenant.

2. Once you are logged into your tenant, click on the **Credential Providers** tab in the left sidebar. You are directed to the Credential Providers page displaying a list of existing Credential Providers. In this example, there are no existing Credential Providers.

   ![Credential Providers - Main Page Empty](https://docs.aembit.io/_astro/credential_providers_main_page_empty.BTUxwZGj_ZknLOA.webp)

3. Click on the **New** button to open the Credential Providers dialog window.

   ![Credential Providers - Dialog Window Empty](https://docs.aembit.io/_astro/credential_providers_oauth_clientcreds_dialog_window_empty.BqwX6bzl_Z2mtcls.webp)

4. In the Credential Providers dialog window, enter the following information:

   * **Name** - Name of the Credential Provider.

   * **Description** - An optional text description of the Credential Provider.

   * **Credential Type** - A dropdown menu that enables you to configure the Credential Provider type. Select **OAuth 2.0 Client Credentials**.

   * **Token Endpoint Url** - The Token Endpoint URL is the designated location where an application can obtain an access token through the OAuth 2.0 Client Credentials Flow.

   * **Client Id** - The Client ID is a unique identifier assigned to your application upon registration. You can find your application’s Client ID in the respective section provided by the OAuth Server.

   * **Client Secret** - The Client Secret is a secret that is only known to the client (application) and the Authorization Server. It is used for secure authentication between the client and the Authorization Server.

   * **Scopes (optional)** - OAuth 2.0 allows clients to specify the level of access they require while seeking authorization. Typically, scopes are documented by the server to inform clients about the access required for specific actions.

   * **Credential Style** - A set of options that allows you to choose how the credentials are sent to the authorization server when requesting an access token. You can select one of the following options:

     * **Authorization Header** - The credentials are included in the request’s Authorization header as a Base64-encoded string. This is the most common and secure method.
     * **POST Body** - The credentials are sent in the body of the POST request as form parameters. This method is less common and may be required by certain servers that don’t support the Authorization header.

   Make sure to review your Server Workload documentation to determine what is considered the credential style in that specific context.

   ![Credential Providers - Dialog Window Completed](https://docs.aembit.io/_astro/credential_providers_oauth_clientcreds_dialog_window_completed.DC9FqqKa_Z1hUOA4.webp)

5. Click **Save** when finished. You will be directed back to the Credential Providers page, where you will see your newly created Credential Provider.

   ![Credential Providers - Main Page With New Credential Provider](https://docs.aembit.io/_astro/credential_providers_oauth_clientcreds_main_page_with_new_credential_provider.DD7Pbz3y_Z1sxATY.webp)

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

* [Apigee](../server-workloads/guides/apigee.md)
* [Beyond Identity](../server-workloads/guides/beyond-identity.md)
* [Box](../server-workloads/guides/box.md)
* [Databricks](../server-workloads/guides/databricks.md)
* [GCP BigQuery](../server-workloads/guides/gcp-bigquery.md)
* [GitHub REST](../server-workloads/guides/github-rest.md)
* [GitLab REST](../server-workloads/guides/gitlab-rest.md)
* [HashiCorp Vault](../server-workloads/guides/hashicorp-vault.md)
* [Microsoft Graph](../server-workloads/guides/microsoft-graph.md)
* [Okta](../server-workloads/guides/okta.md)
* [PayPal](../server-workloads/guides/paypal.md)
* [Salesforce REST](../server-workloads/guides/salesforce-rest.md)
* [Slack](../server-workloads/guides/slack.md)
