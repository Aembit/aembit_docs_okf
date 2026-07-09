---
type: how-to
title: "Create an Access Policy"
description: "How to create an Access Policy using the Access Policy Builder interface"
resource: https://docs.aembit.io/user-guide/access-policies/create-access-policy/
interface: web-ui
tags: [access-policy]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Create an Access Policy

This guide walks you through creating an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../get-started/concepts/access-policies.md) using the Access Policy Builder. The example creates an AWS cloud-native policy that allows EC2 instances in Washington State to access AWS S3 buckets.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

* Access to the Aembit Admin UI
* Appropriate permissions to create Access Policies and their components

Feature flag

To use the Access Policy Builder described in this guide, enable **Use new access policy** in your user preferences.

How to enable the feature flag

1. In the Aembit Admin UI, click your username in the bottom of the left sidebar.

2. Select **Profile** from the dropdown menu.

3. Select **Preferences** section, toggle on **Use new access policy**.

4. Enable **Access Policy: Use new access policy** in the **User Interface** section.

   ![User Profile Preferences tab with Access Policy enabled](https://docs.aembit.io/_astro/feature-flag-access-policy-builder.BXlhOeck_ZIFpEe.webp)

## Open the Access Policy Builder

[Section titled “Open the Access Policy Builder”](#open-the-access-policy-builder)

1. In the Aembit Admin UI, select **Access Policies** from the left sidebar.

   ![Access Policies list page showing the main navigation and policy table](https://docs.aembit.io/_astro/apb-access-policies-list.B7z6pV7Z_u9Wjn.webp)

2. Click **+ New** to open the Access Policy Builder.

   ![Access Policy Builder initial view with card-based navigation and configuration panel](https://docs.aembit.io/_astro/apb-builder-initial.C90bKxHU_Z2ske4d.webp)

The Access Policy Builder displays a component card for each part of the policy in the right panel. An **Access Policy** status card on the left summarizes the policy and tracks each component’s configuration status. Configure each component from its card in the right panel:

* **Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../get-started/concepts/client-workloads.md)** (Required) - The application requesting access
* **Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../get-started/concepts/server-workloads.md)** (Required) - The service being accessed
* **Trust Providers**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../get-started/concepts/trust-providers.md)** (Recommended) - Identity verification method
* **Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../../get-started/concepts/access-conditions.md)** (Recommended) - Additional access constraints
* **Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../get-started/concepts/credential-providers.md)** - How credentials are obtained

Change the requirement of each Access Policy component based on your organization’s compliance needs, using [Global Policy Compliance](../administration/global-policy/overview.md).

## Configure the Access Policy details

[Section titled “Configure the Access Policy details”](#configure-the-access-policy-details)

The **Access Policy** panel displays by default when you open the builder, so you can name and save the policy first.

1. In the **Name** field, enter a name for your Access Policy.

2. (Optional) In the **Description** field, add a description to help identify the policy’s purpose.

3. (Optional) In the **Tags** section, click **+ New Tag** to add tags for organization.

   ![Access Policy details panel with name, description, and tags fields](https://docs.aembit.io/_astro/apb-policy-details-filled.CDsJDrLy_2uY2A2.webp)

## Add a Client Workload

[Section titled “Add a Client Workload”](#add-a-client-workload)

In the **Client Workload** card in the right panel, click **+ Configure** to configure the client application.

Each component in the Access Policy Builder offers two options:

* **Add New** - Create a new component directly within the builder. The component saves to your tenant and associates with this policy.
* **Select Existing** - Choose from components you’ve already created. This lets you reuse components across multiple policies.

For detailed information about Client Workload configuration options and identification types, see [Client Workloads](client-workloads/overview.md).

* Add New

  To create a new Client Workload:

  1. Select the **Add New** tab if not already selected.

     ![Client Workload Add New form with name and identification fields](https://docs.aembit.io/_astro/apb-client-workload-add-new.Be-4NO4W_Z1z3gfB.webp)

  2. In the **Name** field, enter a name for the Client Workload.

  3. (Optional) In the **Description** field, add context about the workload.

  4. From the **Client Identification** dropdown, select an identification type. For AWS EC2 instances, select **AWS EC2 Instance Id**.

  5. In the **Value** field, enter the identification value (for example, `i-0abc123def456789a`).

  6. (Optional) Click **+ Additional Client Identifier** to add more identifiers.

  7. Click **Save** to add the Client Workload to the policy.

     ![Client Workload configured and ready to save](https://docs.aembit.io/_astro/apb-client-workload-add-new-configured.vW_iK_Vc_ifi6d.webp)

* Select Existing

  To use an existing Client Workload:

  1. Select the **Select Existing** tab.

     ![Client Workload Select Existing view with searchable table](https://docs.aembit.io/_astro/apb-client-workload-select-existing.gINPYsN6_NtbGy.webp)

  2. Use the search field to filter the list.

  3. Click a row to select a Client Workload. The selected row highlights with an orange border.

     ![Client Workload selected with orange highlight](https://docs.aembit.io/_astro/apb-client-workload-select-existing-row-selected.c-fAlXhy_Z1F3Kqf.webp)

  4. Click **Use Selected** to add it to the policy.

## Add a Server Workload

[Section titled “Add a Server Workload”](#add-a-server-workload)

In the **Server Workload** card in the right panel, click **+ Configure** to configure the target service.

For detailed information about Server Workload configuration options, protocols, and authentication methods, see [Server Workloads](server-workloads/overview.md).

* Add New

  To create a new Server Workload:

  1. Select the **Add New** tab if not already selected.

     ![Server Workload Add New form with service endpoint fields](https://docs.aembit.io/_astro/apb-server-workload-add-new.0cvb0JJu_Z19f0GO.webp)

  2. In the **Name** field, enter a name for the Server Workload (for example, `AWS S3 Storage Bucket`).

  3. (Optional) In the **Description** field, add context about the workload.

  4. In the **Service Endpoint** section, configure the connection details:

     * **Host**: Enter the service hostname (for example, `s3.us-west-2.amazonaws.com`).
     * **Application Protocol**: Select the protocol (for example, **HTTP**).
     * **Transport Protocol**: Select **TCP** (default).
     * **Port**: Enter the port number (for example, `443`). This field auto-populates based on the selected protocol.
     * **TLS**: Select this checkbox for secure connections.
     * **Forward to Port**: (Optional) Enter the destination port if different from the incoming port.

  5. (Optional) From the **Authentication Method** dropdown, select an authentication method if the server requires it.

  6. Click **Save** to add the Server Workload to the policy.

     ![Server Workload configured with endpoint and authentication settings](https://docs.aembit.io/_astro/apb-server-workload-add-new-configured.DpHdZuGd_2rSwSJ.webp)

* Select Existing

  To use an existing Server Workload:

  1. Select the **Select Existing** tab.

     ![Server Workload Select Existing view with searchable table](https://docs.aembit.io/_astro/apb-server-workload-select-existing.C3TUt0rJ_13I7of.webp)

  2. Use the search field to filter the list.

  3. Click a row to select a Server Workload. The selected row highlights with an orange border.

     ![Server Workload selected with orange highlight](https://docs.aembit.io/_astro/apb-server-workload-select-existing-row-selected.CkZ4YqtD_tKTdx.webp)

  4. Click **Use Selected** to add it to the policy.

## Add a Trust Provider

[Section titled “Add a Trust Provider”](#add-a-trust-provider)

In the **Trust Provider** card in the right panel, click **+ Configure** to configure identity verification.

For detailed information about Trust Provider types and match rule configuration, see [Trust Providers](trust-providers/overview.md).

* Add New

  To create a new Trust Provider:

  1. Select the **Add New** tab if not already selected.

     ![Trust Provider Add New form with provider type selection](https://docs.aembit.io/_astro/apb-trust-provider-add-new.CxQXgMqH_1bc1Nl.webp)

  2. In the **Name** field, enter a name for the Trust Provider.

  3. (Optional) In the **Description** field, add context about the provider.

  4. From the **Trust Provider** dropdown, select a provider type:

     * **AWS Metadata Service** - For AWS EC2 instance identity verification
     * **AWS Role** - For AWS Identity and Access Management (IAM) role-based trust
     * **Azure Instance Metadata Service** - For Azure Virtual Machine (VM) identity
     * **Certificate Signed Attestation** - For cryptographically signed attestation documents
     * **GCP Identity Token** - For Google Cloud Platform (GCP) identity
     * **GitHub Action ID Token** - For GitHub Actions workflows
     * **GitLab Job ID Token** - For GitLab CI/CD pipelines
     * **Kerberos** - For Active Directory environments
     * **Kubernetes Service Account** - For Kubernetes workload identity
     * **OIDC ID Token** - For generic OpenID Connect (OIDC) providers
     * **SAMLv2 Response** - For SAML identity provider responses
     * **Terraform Cloud Identity Token** - For Terraform Cloud workspaces

  5. Configure the type-specific settings. For most provider types, configure **Match Rules** to specify which identity claims to verify.

  6. Click **Save** to add the Trust Provider to the policy.

     ![Trust Provider configured with match rules](https://docs.aembit.io/_astro/apb-trust-provider-add-new-configured.B5EKlGbR_1y09OD.webp)

  To add multiple Trust Providers, click **+ Add Another** after saving the first one, then choose **Add New** to create another provider or **Select Existing** to choose from existing providers.

* Select Existing

  To use an existing Trust Provider:

  1. Select the **Select Existing** tab.

     ![Trust Provider Select Existing view with searchable table](https://docs.aembit.io/_astro/apb-trust-provider-select-existing.BNxeadPu_Zjeqcy.webp)

  2. Use the search field to filter the list.

  3. Click a row to select a Trust Provider. The selected row highlights with an orange border.

     ![Trust Provider selected with orange highlight](https://docs.aembit.io/_astro/apb-trust-provider-select-existing-row-selected.eYuUtM7C_Z1OLj3.webp)

  4. Click **Use Selected** to add it to the policy.

## Add Access Conditions (optional)

[Section titled “Add Access Conditions (optional)”](#add-access-conditions-optional)

In the **Access Conditions** card in the right panel, click **+ Configure** to add optional access constraints. Access Conditions provide additional security by restricting access based on factors like geographic location or time of day.

For detailed information about Access Condition types and integration options, see [Access Conditions](access-conditions/overview.md).

Unlike the other components, the Access Conditions panel has no **Add New** or **Select Existing** tabs. You configure the condition directly in the panel.

1. In the **Name** field, enter a name for the Access Condition (for example, `Washington State Location`).

   ![Access Condition form with integration selection](https://docs.aembit.io/_astro/apb-access-condition-add-new.Cq2N7GQb_Z1aUQhD.webp)

2. (Optional) In the **Description** field, add context about the condition.

3. From the **Integration** dropdown, select a condition type:

   * **Aembit GeoIP Condition** - Restrict access based on geographic location
   * **Aembit Time Condition** - Restrict access based on time windows
   * Other third-party integrations as configured in your tenant

4. Configure the integration-specific settings. For GeoIP conditions:

   * Click **Add Country** to add a location rule.
   * From the **Country** dropdown, select a country (for example, `United States of America`).
   * (Optional) From the **Subdivision** dropdown, select a specific state or region (for example, `Washington`).

5. Click **Save** to add the Access Condition to the policy.

   ![Access Condition configured with geographic restrictions](https://docs.aembit.io/_astro/apb-access-condition-add-new-configured.BUgDBqvO_1hlrzg.webp)

## Add a Credential Provider

[Section titled “Add a Credential Provider”](#add-a-credential-provider)

In the **Credential Provider** card in the right panel, click **+ Configure** to configure how the policy obtains credentials for accessing the Server Workload.

For detailed information about Credential Provider types and configuration options, see [Credential Providers](credential-providers/overview.md).

* Add New

  To create a new Credential Provider:

  1. Select the **Add New** tab if not already selected.

     ![Credential Provider Add New form with credential type selection](https://docs.aembit.io/_astro/apb-credential-provider-add-new.C2SNjmEJ_1lzf4T.webp)

  2. In the **Name** field, enter a name for the Credential Provider (for example, `AWS S3 Access Credential`).

  3. (Optional) In the **Description** field, add context about the credential.

  4. From the **Credential Type** dropdown, select a credential type:

     * **Aembit Access Token** - For Aembit-native authentication
     * **API Key** - For static API key credentials
     * **AWS Secrets Manager Value** - For credentials stored in AWS Secrets Manager
     * **AWS Security Token Service Federation** - For AWS STS AssumeRole credentials
     * **Azure Entra Identity Federation** - For Azure identity federation
     * **Azure Key Vault Secret Value** - For credentials stored in Azure Key Vault
     * **Google Workload Identity Federation** - For GCP identity federation
     * **OAuth 2.0 Client Credentials** - For OAuth client credentials flow

  5. Configure the type-specific settings. For AWS Security Token Service Federation:

     * **OIDC Issuer URL**: Auto-populated with your tenant’s identity URL.
     * **AWS IAM Role Arn**: Enter the Amazon Resource Name (ARN) of the IAM role to assume (for example, `arn:aws:iam::123456789012:role/AembitS3AccessRole`).
     * **Aembit IdP Token Audience**: The Identity Provider (IdP) token audience, auto-populated with `sts.amazonaws.com`.
     * **Lifetime**: Set the credential lifetime in seconds (default: `3600`).

  6. Click **Save** to add the Credential Provider to the policy.

     ![Credential Provider configured with AWS STS settings](https://docs.aembit.io/_astro/apb-credential-provider-add-new-configured.CiVmfWM3_1rMPxk.webp)

* Select Existing

  To use an existing Credential Provider:

  1. Select the **Select Existing** tab.

     ![Credential Provider Select Existing view with searchable table](https://docs.aembit.io/_astro/apb-credential-provider-select-existing.DzLQ5dzG_K8uuI.webp)

  2. Use the search field to filter the list.

  3. Click a row to select a Credential Provider. The selected row highlights with an orange border.

     ![Credential Provider selected with orange highlight](https://docs.aembit.io/_astro/apb-credential-provider-select-existing-row-selected.fD93kNDQ_Z2cH7EA.webp)

  4. Click **Use Selected** to add it to the policy.

## Save the Access Policy

[Section titled “Save the Access Policy”](#save-the-access-policy)

After configuring all required components, you can save the Access Policy.

1. In the **Access Policy** status card on the left, confirm each required component shows a green **Configured** status. Click a component in the status card to jump to its details on the right:

   * Client Workload
   * Server Workload
   * Trust Provider
   * Credential Provider

   ![All components configured with green checkmarks](https://docs.aembit.io/_astro/apb-all-components-configured.Dv-e6kt__Z2osngJ.webp)

2. In the header, click **Save Policy** to save the policy without activating it, or click **Save Policy & Activate** to save and activate it in one step.

   ![Access Policy saved from the Access Policy Builder header](https://docs.aembit.io/_astro/apb-policy-created.Lgg_r31Y_Z2m2jN2.webp)

The Access Policy now governs access from the configured Client Workload to the Server Workload based on the Trust Provider verification, Access Conditions, and Credential Provider settings.

Allow time for changes to take effect

Access Policy changes apply in near-real-time, but not instantly. After you create, edit, or activate a policy, allow a short time for the change to take effect.
