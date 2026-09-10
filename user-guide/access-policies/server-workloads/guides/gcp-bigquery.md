---
type: how-to
title: "GCP BigQuery"
description: "This page describes how to configure Aembit to work with the GCP BigQuery Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/gcp-bigquery/
interface: web-ui
tags: ["data-analytics", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# GCP BigQuery

[Google BigQuery](https://cloud.google.com/bigquery?hl=en), part of Google Cloud Platform, is a data warehousing solution designed for storing, querying, and analyzing large datasets. It offers scalability, SQL-based querying, and integrations with other GCP services and third-party tools.

Below you can find the Aembit configuration required to work with the GCP BigQuery service as a

Server Workload using the BigQuery REST API.

Aembit supports multiple authentication/authorization methods for BigQuery. This page describes scenarios where the Credential Provider is configured for BigQuery via:

* [OAuth 2.0 Authorization Code (3-legged OAuth)](gcp-bigquery.md#oauth-20-authorization-code)
* [Google Workload Identity Federation](gcp-bigquery.md#google-workload-identity-federation)

> **Prerequisites**
>
> Before proceeding with the configuration, ensure you have the following:
>
> * An active Google Cloud account
> * A GCP project with BigQuery enabled
> * Data available for querying in BigQuery

## OAuth 2.0 authorization code

### Create the OAuth client ID

1. Sign in to the Google Cloud console and go to the [Credentials](https://console.cloud.google.com/apis/credentials) page. Confirm you are working in a project where you have authorization.

2. Click **Create Credentials**, then select **OAuth client ID**.

   ![Google Cloud Credentials page with the Create Credentials menu and the OAuth client ID option](https://docs.aembit.io/_astro/gcp_create_oauth_client_id.Bslva-4Y_2chrWM.webp)

3. If your project has no consent screen, click **Configure Consent Screen**.

   ![Prompt to configure the OAuth consent screen before creating an OAuth client ID](https://docs.aembit.io/_astro/gcp_no_consent_screen.ByBGUKd3_2mifI7.webp)

4. Choose a **User Type**, then click **Create**.

5. Enter an app name, choose a user support email, and enter a developer contact email, then click **Save and Continue**.

6. Click **Save and Continue** to skip the **Scopes** step, then review the **Summary** and click **Back to Dashboard**.

7. Return to the [Credentials](https://console.cloud.google.com/apis/credentials) page, click **Create Credentials**, and select **OAuth client ID** again.

8. For **Application type**, select **Web application**, then enter a name for the client. Leave the **Authorized redirect URIs** field empty for now; you add the Aembit Callback URL in the next section. Keep the Google Cloud console open.

### Configure the Credential Provider

Create and authorize an OAuth 2.0 Authorization Code Credential Provider in Aembit. For the full field reference and the authorization flow, see [Configure OAuth 2.0 Authorization Code Credential Provider](../../credential-providers/oauth-authorization-code.md).

1. In your Aembit Tenant, create a Credential Provider, select the **OAuth 2.0 Authorization Code** credential type, then copy the read-only **Callback URL**.

2. Return to the Google Cloud console, paste the **Callback URL** into the **Authorized redirect URIs** field, then click **Create**.

3. In the pop-up, copy both the **Client ID** and the **Client Secret**, and store them for the next step.

4. Back in Aembit, configure the following BigQuery-specific fields, then click **Save**:

   | Field             | Value                                          |
   | ----------------- | ---------------------------------------------- |
   | **Client ID**     | The Client ID you copied from Google Cloud     |
   | **Client Secret** | The Client Secret you copied from Google Cloud |
   | **Scopes**        | `https://www.googleapis.com/auth/bigquery`     |
   | **OAuth URL**     | `https://accounts.google.com`                  |
   | **PKCE Required** | Off                                            |
   | **Lifetime**      | 1 year                                         |

   For **OAuth URL**, click **URL Discovery** to populate the Authorization URL and Token URL. For other scopes, see [OAuth 2.0 Scopes for Google APIs](https://developers.google.com/identity/protocols/oauth2/scopes). For **Lifetime**, Google issues a 7-day refresh token to projects with an external user type and a publishing status of Testing. Google doesn’t specify a refresh token lifetime for the internal user type. See the [Google documentation on token expiration](https://developers.google.com/identity/protocols/oauth2#expiration) for details.

5. Click **Authorize**, then choose your Google Account and click **Allow**. The Credential Provider status changes to **Ready** when the flow completes.

### Create the Server Workload

1. Create a new Server Workload.

2. Configure the following fields:

   | Field                     | Value                     |
   | ------------------------- | ------------------------- |
   | **Name**                  | A user-friendly name      |
   | **Host**                  | `bigquery.googleapis.com` |
   | **Application Protocol**  | HTTP                      |
   | **Port**                  | 443 with TLS              |
   | **Forward to Port**       | 443 with TLS              |
   | **Authentication method** | HTTP Authentication       |
   | **Authentication scheme** | Bearer                    |

### Create an Access Policy

Create an Access Policy linking your Client Workload, the OAuth 2.0 Authorization Code Credential Provider, and the Server Workload. See [Access Policies](../../overview.md) for details.

## Google workload identity federation

### Configure the Credential Provider

Follow the complete setup guide for the Google Workload Identity Federation Credential Provider:

[Configure a Google GCP WIF Credential Provider](../../credential-providers/google-workload-identity-federation.md)

This guide covers:

* Creating the Credential Provider in Aembit
* Setting up a service account in Google Cloud
* Creating a Workload Identity Federation pool and OIDC provider, including the attribute mapping
* Granting the pool access to the service account
* Completing the Credential Provider and verifying the connection

### Create the Server Workload

1. Create a new Server Workload.

2. Configure the following fields:

   | Field                     | Value                     |
   | ------------------------- | ------------------------- |
   | **Name**                  | A user-friendly name      |
   | **Host**                  | `bigquery.googleapis.com` |
   | **Application Protocol**  | HTTP                      |
   | **Port**                  | 443 with TLS              |
   | **Forward to Port**       | 443 with TLS              |
   | **Authentication method** | HTTP Authentication       |
   | **Authentication scheme** | Bearer                    |

### Create an Access Policy

Create an Access Policy linking your Client Workload, the Google Workload Identity Federation Credential Provider, and the Server Workload. See [Access Policies](../../overview.md) for details.

## Client Workload configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it’s possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit overwrites these placeholder credentials with the appropriate ones during the access process.

## Required features

* You must configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the GCP BigQuery Server Workload.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
