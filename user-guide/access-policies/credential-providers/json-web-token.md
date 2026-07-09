---
type: how-to
title: "Configure a JSON Web Token (JWT) Credential Provider"
description: "How to create and use a JSON Web Token (JWT) Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/json-web-token/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-30T13:30:29-04:00
---

# Configure a JSON Web Token (JWT) Credential Provider

A JSON Web Token (JWT), defined by the open standard [RFC 7519](https://datatracker.ietf.org/doc/html/rfc7519), is a compact and self-contained method for securely transmitting information as a JSON object between parties.

Aembit’s JWT Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) currently supports Snowflake Key Pair Authentication for connecting to Snowflake Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../get-started/concepts/server-workloads.md).

Multiple providers

You can configure multiple JWT Credential Providers within a single Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../get-started/concepts/access-policies.md) to support different users or services accessing the same Server Workload. See [Configure multiple JWT Credential Providers](#configure-multiple-jwt-credential-providers) for the procedure, or [Using multiple JWT Credential Providers](json-web-token-multiple.md) for conceptual details.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before configuring a JWT Credential Provider in Aembit, ensure you have the following:

* An active Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../get-started/concepts/administration.md) with appropriate permissions to create and manage Credential Providers.
* A Snowflake account with permissions to configure key pair authentication.

## Credential Provider configuration

[Section titled “Credential Provider configuration”](#credential-provider-configuration)

To configure a JSON Web Token (JWT) Credential Provider, follow these steps:

1. Log into your Aembit Tenant and go to **Credential Providers**.

   Aembit directs you to the **Credential Providers** page displaying a list of existing Credential Providers.

   ![Credential Providers - Main Page Empty](https://docs.aembit.io/_astro/credential_providers_main_page_empty.BTUxwZGj_ZknLOA.webp)

2. Click **+ New** to open the Credential Providers dialog window.

   ![Credential Providers - Dialog Window Empty](https://docs.aembit.io/_astro/credential_providers_jwt_dialog_window_empty.D6tayh2z_ZCS8VQ.webp)

3. In the Credential Providers dialog window, enter the following information:

   * **Name** - Name of the Credential Provider.

   * **Description** - An optional text description of the Credential Provider.

   * **Credential Type** - Select **JSON Web Token (JWT)** from the dropdown menu.

   * **Token Configuration** - By default, this field is pre-selected as **Snowflake Key Pair Authentication** for connecting to Snowflake.

   * **Snowflake Account ID** - The Snowflake Locator, a unique identifier that distinguishes a Snowflake account within the organization.

   * **Username** - Your unique Snowflake username associated with the account.

   * **Snowflake Alter User Command** - After saving the Credential Provider, Aembit generates a SQL command in this field. This command incorporates a public key essential for establishing trust between your Snowflake account and the JWT tokens issued by Aembit. Execute this command on your Snowflake account using a Snowflake-compatible tool.

   ![Credential Providers - Dialog Window Completed](https://docs.aembit.io/_astro/credential_providers_jwt_dialog_window_completed.CcAszLZ9_Z1JQ7Ib.webp)

4. Click **Save** when finished. Aembit directs you back to the **Credential Providers** page, where you see your newly created Credential Provider.

   ![Credential Providers - Main Page With New Credential Provider](https://docs.aembit.io/_astro/credential_providers_jwt_main_page_with_new_credential_provider.Yj7FVU2Y_Z25vBV1.webp)

## Configure multiple JWT Credential Providers

[Section titled “Configure multiple JWT Credential Providers”](#configure-multiple-jwt-credential-providers)

To configure multiple JWT Credential Providers within a single Access Policy, follow these steps. Each Credential Provider must have a unique mapping value (username for Snowflake, or HTTP header/body value for HTTP workloads).

How it works

For conceptual information about how Aembit routes requests to the appropriate Credential Provider, see [Using multiple JWT Credential Providers](json-web-token-multiple.md).

### Prerequisites

[Section titled “Prerequisites”](#prerequisites-1)

Before configuring multiple JWT Credential Providers, ensure you have:

* An existing Access Policy with a Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../get-started/concepts/client-workloads.md) and Server Workload configured
* Server Workload Application Protocol set to **Snowflake** or **HTTP**
* At least two JWT Credential Providers created (or ready to create)

### Add multiple JWT Credential Providers to an Access Policy

[Section titled “Add multiple JWT Credential Providers to an Access Policy”](#add-multiple-jwt-credential-providers-to-an-access-policy)

1. Create your first JWT Credential Provider by following the [Credential Provider configuration](#credential-provider-configuration) procedure.

2. Note the mapping value for this Credential Provider (Snowflake username or the HTTP header/body value you plan to use).

3. Repeat the Credential Provider configuration steps to create additional JWT Credential Providers, each with a unique mapping value.

4. Go to **Access Policies** and open the policy in the Access Policy Builder (create a new policy or edit an existing one).

5. In the **Credential Provider** card, configure your first JWT Credential Provider (or select an existing one).

6. To add each additional JWT Credential Provider, click **+ Add Another** on the Credential Provider card. In the **Credential Mapping** dialog, click **Continue**, then add the Credential Provider on the **Add New** or **Select Existing** tab.

   Caution

   When you add additional Credential Providers to an Access Policy, you must also map each Credential Provider so Aembit can route requests correctly.

7. Map each Credential Provider to its selector value, as described in [Map JWT Credential Providers](#map-jwt-credential-providers).

### Map JWT Credential Providers

[Section titled “Map JWT Credential Providers”](#map-jwt-credential-providers)

After adding multiple JWT Credential Providers to an Access Policy, map each Credential Provider to its selector value.

* Snowflake

  1. On the Access Policy page, in the **Credential Providers** column, click the arrow to open the Credential Provider Mappings dialog window.

  2. For each Credential Provider with a red ”!” icon (indicating no mapping), hover over the Credential Provider and click the down arrow to open the mapping menu.

     ![Credential Provider Mappings Dropdown](https://docs.aembit.io/_astro/multiple_credential_providers_mapping_page_credential_provider_dropdown.Bgu25Zek_1YL5WN.webp)

  3. Add the Snowflake usernames that should use this Credential Provider. When a connection request arrives with this username, Aembit uses this Credential Provider for credential injection.

  4. Click **Save** when you finish adding mapping values. The red ”!” icon changes to a green checkbox.

  5. Repeat for each Credential Provider in the Access Policy.

  6. When all Credential Providers show “All Mapped”, click **Save** or **Save Policy & Activate** to save your Access Policy.

* HTTP

  1. On the Access Policy page, in the **Credential Providers** column, click the arrow to open the Credential Provider Mappings dialog window.

  2. For each Credential Provider with a red ”!” icon (indicating no mapping), hover over the Credential Provider and click the down arrow to open the mapping menu.

     ![Credential Provider Menu HTTP Mapping](https://docs.aembit.io/_astro/multiple_credential_providers_credential_provider_mappings_mapping_type_http.DWZIBd0P_Z29FonO.webp)

  3. Select the mapping type (**HTTP Header** or **HTTP Body**) and add the values that should use this Credential Provider. When a request arrives with these values, Aembit uses this Credential Provider for credential injection.

     ![Credential Provider Mapping Dialog With HTTP Header and HTTP Body](https://docs.aembit.io/_astro/multiple_credential_providers_credential_provider_mappings_dialog_http.E75S4pol_Z1xrXMv.webp)

  4. Click **Save** when you finish adding mapping values. The red ”!” icon changes to a green checkbox.

  5. Repeat for each Credential Provider in the Access Policy.

  6. When all Credential Providers show “All Mapped”, click **Save** or **Save Policy & Activate** to save your Access Policy.

### Verify your configuration

[Section titled “Verify your configuration”](#verify-your-configuration)

To confirm your multiple JWT Credential Provider configuration works correctly:

1. Make a request using one of your mapped values (Snowflake username or HTTP header/body value).

2. Check the [access authorization events](../../audit-report/access-authorization-events.md) in your Aembit Tenant to confirm:

   * Aembit selected the correct Credential Provider
   * The `credentialProvider.name` field matches your expected Credential Provider

3. Make a request using a different mapped value and repeat to verify the second Credential Provider.

## Related topics

[Section titled “Related topics”](#related-topics)

* [Using multiple JWT Credential Providers](json-web-token-multiple.md) - Learn how Aembit routes requests to multiple JWT Credential Providers
* [Configure multiple Credential Providers](multiple-credential-providers.md) - Overview of multiple Credential Provider support
* [Snowflake Server Workload](../server-workloads/guides/snowflake.md) - Configure Aembit to work with Snowflake
* [Credential Providers overview](overview.md) - Overview of all available Credential Provider types
* [Access Policies](../overview.md) - Learn about Aembit Access Policies and how they work
* [Access Authorization Events](../../audit-report/access-authorization-events.md) - Review access authorization event information in the Reporting Dashboard

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

* [Snowflake](../server-workloads/guides/snowflake.md)
