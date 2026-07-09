---
type: how-to
title: "GCP Identity Token"
description: "How to identify GCP workloads using the service account email from a GCP Identity Token in Aembit"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/gcp-identity-token/
interface: web-ui
tags: [gcp, identification, client-workload, access-policy]
timestamp: 2025-06-26T14:10:42-07:00
type_inferred: true
---

# GCP Identity Token

This page explains how to use the **GCP Identity Token** identifier to uniquely identify workloads running on **Google Cloud Platform (GCP)** using a GCP Identity Token.

## Understanding the GCP identity token identifier

[Section titled “Understanding the GCP identity token identifier”](#understanding-the-gcp-identity-token-identifier)

When you run workloads as a GCP Function or Cloud Run job, the platform issues a [GCP Identity Token](https://cloud.google.com/docs/authentication/token-types#id) that includes an `email` claim. This email corresponds to the service account the workload runs under.

For example, a service account might look like: `123456789012-compute@developer.gserviceaccount.com`

Aembit identifies the workload using this email claim. Aembit supports this approach **only in the Edge CLI** at this time and isn’t available **for Edge Proxy**.

## Applicable deployment type

[Section titled “Applicable deployment type”](#applicable-deployment-type)

Aembit supports the GCP Identity Token identifier for Edge-based deployments running the Edge CLI on GCP Function or GCP Cloud Run job.

## Create a Client Workload with a GCP identity token identifier

[Section titled “Create a Client Workload with a GCP identity token identifier”](#create-a-client-workload-with-a-gcp-identity-token-identifier)

To configure a Client Workload using the GCP Identity Token identifier, follow these steps:

1. Log into your Aembit Tenant.

2. Click **Client Workloads** in the left nav pane.

3. Click **New**, revealing the **Client Workload** pop out menu.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **GCP Identity Token**.

   For **Value**, enter the email associated with the GCP service account under which the workload runs.

   For example: `123456789012-compute@developer.gserviceaccount.com`

6. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Find the service account email

[Section titled “Find the service account email”](#find-the-service-account-email)

The service account email identifies your workload and its format depends on the specific GCP service you’re using. Common patterns include:

* **Cloud Functions (Gen 1):**\
  `<project-id>@appspot.gserviceaccount.com`

* **Cloud Functions (Gen 2):**\
  `<project-number>@developer.gserviceaccount.com`

* **Cloud Run Jobs:**\
  `<project-number>@developer.gserviceaccount.com`

You can find both the **project ID** and **project number** in the GCP Console by going to **Cloud Overview** > **Dashboard**. They appear in the project info card at the top of the page.

To view the actual service accounts and their associated emails, navigate to **IAM & Admin** > **Service Accounts** in the GCP Console.

## Related

**Compatible trust providers**

* [GCP Identity Token](../../trust-providers/gcp-identity-token-trust-provider.md)
