---
type: how-to
title: "Configure a Google GCP WIF Credential Provider"
description: "How to create a Google GCP Workload Identity Federation (WIF) Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/google-workload-identity-federation/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2026-06-09T18:19:33-07:00
type_inferred: true
---

# Configure a Google GCP WIF Credential Provider

Aembit’s Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../get-started/concepts/credential-providers.md) for Google Workload Identity Federation (WIF) lets your workloads obtain short-lived Google Cloud credentials through Aembit acting as a third-party federated Identity Provider (IdP). Use it to authenticate to any Google Cloud service, such as BigQuery, Vertex AI, or Cloud Storage, without managing long-lived service account keys.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To configure a Google Workload Identity Federation Credential Provider, you must have the following:

* Access to your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../get-started/concepts/administration.md) with permission to manage Credential Providers.

* Access to a Google Cloud project where you can manage IAM, service accounts, and Workload Identity Federation.

## Create the Credential Provider

[Section titled “Create the Credential Provider”](#create-the-credential-provider)

Create the Credential Provider in Aembit first, so you can copy its OIDC Issuer URL into Google Cloud during setup.

1. Log in to your Aembit Tenant, and in the left sidebar, click **Credential Providers**.

2. Click **+ New** to open the **Credential Provider** dialog.

3. Enter a **Name** and an optional **Description**.

4. In the **Credential Type** dropdown, select **Google Workload Identity Federation**, which reveals the remaining fields.

5. Copy the auto-generated **OIDC Issuer URL** and store it for later. You paste this value into Google Cloud when you add the Workload Identity Federation provider.

   ![Aembit Credential Provider dialog with Google Workload Identity Federation selected as the Credential Type and the auto-generated OIDC Issuer URL displayed](https://docs.aembit.io/_astro/credential_providers_google_wif_dialog_window_empty.C4ClBm0z_2gwPpj.webp)

   Leave the **Audience** and **Service Account Email** fields empty for now, and keep the Aembit web UI open. You complete these fields after you finish the Google Cloud setup in the following sections.

## Set up the service account

[Section titled “Set up the service account”](#set-up-the-service-account)

Create a service account for Aembit to impersonate, then grant it the IAM roles your workloads need.

1. Sign in to the [Google Cloud console](https://console.cloud.google.com/iam-admin/serviceaccounts), and confirm you are working in a project where you have authorization.

2. On the **Service Accounts** page, click **Create Service Account**.

   ![Google Cloud Service Accounts page with the Create Service Account button highlighted](https://docs.aembit.io/_astro/gcp_bigquery_create_service_account.DbhFl_H2_Z1U66PA.webp)

3. Enter a name for the service account. Google Cloud generates the ID from the name, but you can edit it; the description is optional.

   ![Service account creation form showing the name, ID, and optional description fields](https://docs.aembit.io/_astro/gcp_bigquery_create_service_account_details.pkNuhiN8_Z2759HB.webp)

4. Click the copy icon next to the **Email address**, and store the email for later.

5. Click **Done**.

6. In the left sidebar, select **IAM**, then click **Grant Access**.

   ![Google Cloud IAM page with the Grant Access button highlighted](https://docs.aembit.io/_astro/gcp_bigquery_grant_access_to_service_acc.LvlDFigA_ZFs1BU.webp)

7. Under **New principals**, start typing your service account name, then select it from the results.

8. In the **Role** dropdown, select the role that fits your workload’s needs, then click **Save**.

   ![IAM Grant Access dialog with a role selected for the service account](https://docs.aembit.io/_astro/gcp_bigquery_set_role_to_service_account.BUN-pK4p_kWDAO.webp)

## Create a workload identity federation pool

[Section titled “Create a workload identity federation pool”](#create-a-workload-identity-federation-pool)

Create a Workload Identity Federation pool and add an OpenID Connect (OIDC) provider that trusts Aembit as the issuer.

1. In the Google Cloud console, go to [Workload Identity Federation](https://console.cloud.google.com/iam-admin/workload-identity-pools). If this is your first pool, click **Get Started**; otherwise, click **Create Pool**.

   ![Google Cloud Workload Identity Federation page with the Create Pool button](https://docs.aembit.io/_astro/gcp_bigquery_create_pool.Cl_ipB78_Z18fLJG.webp)

2. Enter a name for the pool, then click **Continue**. Google Cloud generates the ID from the name, but you can edit it; the description is optional.

3. For the provider, select **OpenID Connect (OIDC)**, then enter a name for the provider.

4. In the **Issuer (URL)** field, paste the **OIDC Issuer URL** you copied from your Aembit Credential Provider.

5. For the audience, keep **Default audience**, click the **Copy to clipboard** icon next to the generated value, store it for later, then click **Continue**.

   ![Add an OIDC provider to the pool with the issuer URL and default audience configured](https://docs.aembit.io/_astro/gcp_bigquery_add_provider.Chxa1xEk_ZaywFs.webp)

6. On the **Configure provider attributes** step, configure the attribute mapping. In the **OIDC 1** field, enter `assertion.tenant` so it maps to the default **Google 1** attribute, `google.subject`. This maps the Aembit tenant claim to the Google subject that you grant access to in the next section.

   | Google attribute (Google 1) | OIDC attribute (OIDC 1) |
   | --------------------------- | ----------------------- |
   | `google.subject`            | `assertion.tenant`      |

7. Click **Save**.

## Grant the pool access to the service account

[Section titled “Grant the pool access to the service account”](#grant-the-pool-access-to-the-service-account)

Grant the pool’s identities permission to impersonate the service account you created.

1. In the pool you just created, click **Grant Access**.

2. Select **Grant access using Service Account impersonation**, then select your service account from the dropdown.

3. For **Attribute name**, select **subject**.

4. For **Attribute value**, enter your Aembit Tenant ID, then click **Save**. Your Tenant ID is the subdomain of your Aembit URL; for example, in `https://xyz.aembit.io`, the Tenant ID is `xyz`.

   ![Grant pool access to the service account using the subject attribute and the Aembit Tenant ID](https://docs.aembit.io/_astro/gcp_bigquery_grant_access_pool_identity.l_f4MUnm_ZewhH4.webp)

## Complete the Credential Provider in your Aembit Tenant

[Section titled “Complete the Credential Provider in your Aembit Tenant”](#complete-the-credential-provider-in-your-aembit-tenant)

Return to the Aembit web UI you left open, and complete the **Credential Provider** you started:

1. Configure the following fields:

   | Field                     | Value                                                                             |
   | ------------------------- | --------------------------------------------------------------------------------- |
   | **Audience**              | The audience value you copied when you added the OIDC provider                    |
   | **Service Account Email** | The service account email you copied from Google Cloud                            |
   | **Lifetime**              | The duration the credentials remain valid, to a maximum of 3,600 seconds (1 hour) |

   The **Audience** must match either the default audience or one of the provider’s allowed audiences. The default audience is the full canonical resource name of the Workload Identity Pool Provider. The **Service Account Email** has the form `service-account-name@project-id.iam.gserviceaccount.com`.

   ![Completed Aembit Google Workload Identity Federation Credential Provider showing the audience, service account email, and lifetime](https://docs.aembit.io/_astro/gcp_bigquery_wif_credential_provider.DxdT3EiH_Z16cV71.webp)

   Caution

   The default audience value must begin with `//iam.googleapis.com`. If the value you copied from Google Cloud starts with the `https:` scheme, remove it so the value begins with `//`.

2. Click **Save**. Your Google Workload Identity Federation Credential Provider now appears in your list of Credential Providers.

## Verify the connection

[Section titled “Verify the connection”](#verify-the-connection)

Verify that Aembit can exchange tokens with Google Cloud using your new Credential Provider:

1. On the **Credential Providers** page, select the Credential Provider you created.

2. Click **Verify**. After a few moments, a green banner confirms the connection. If verification fails, recheck the audience, the service account email, and the attribute mapping and grant in Google Cloud.

You’re now ready to use your Google Workload Identity Federation Credential Provider with your Server Workloads in an Aembit Access Policy.

## Google Cloud and Credential Provider value mappings

[Section titled “Google Cloud and Credential Provider value mappings”](#google-cloud-and-credential-provider-value-mappings)

The following table shows how each Aembit Credential Provider field maps to a Google Cloud value and where to find it:

| Aembit Credential Provider field | Google Cloud value                       | Where to find it                                                                            |
| -------------------------------- | ---------------------------------------- | ------------------------------------------------------------------------------------------- |
| OIDC Issuer URL                  | Provider **Issuer (URL)**                | Aembit generates this value; you paste it into the OIDC provider when you create the pool   |
| Audience                         | Workload Identity Pool Provider audience | The audience shown when you add the OIDC provider                                           |
| Service Account Email            | Service account email                    | The **Service Accounts** page, or the email you copied when you created the service account |
| Lifetime                         | —                                        | Set in Aembit, to a maximum of 3,600 seconds (1 hour)                                       |
