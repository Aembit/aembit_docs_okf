---
type: how-to
title: "Wiz Integration"
description: "This page describes how to integrate Wiz with Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/integrations/wiz/
interface: web-ui
tags: ["integration", "access-condition", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Wiz Integration

> **Note**
>
> The Wiz Integration feature is a paid feature. To use the Wiz Integration feature, please contact Aembit by completing the [Contact Us form](https://aembit.io/contact/).


The Wiz Cloud Security Platform provides a security analysis service, including inventory enumeration and asset information for identification of customer assets and vulnerabilities. In particular, Wiz provides an Integration API which can be accessed via an OAuth2 Client Credentials Flow and can return an Inventory result set on demand, including Kubernetes Clusters, Deployments, and Vulnerabilities.

## Wiz Integration API

To integrate Wiz with Aembit, you must already have a Wiz API client set up and configured. When setting up your Wiz API client, make sure you request the following information from your Wiz account representative (you will need this information later when integrating with Aembit):

* OAuth2 Endpoint URL
* Client ID
* Client secret
* Audience (this is required and the value is expected to be `wiz-api`)

## Kubernetes/Helm/Agent Proxy Configuration

For the Wiz integration to work correctly, Aembit needs to receive a unique Provider ID that can be compared/matched against the Kubernetes Clusters returned by the Wiz Integration API.

For example, in an AWS EKS Cluster, the output should look similar to the example below:

`arn:aws:eks:region-code:111122223333:cluster/my-cluster`

To use this sample value, update your Aembit Edge Helm Chart deployment with the following parameter values:

* **name** - agentProxy.env.KUBERNETES\_PROVIDER\_ID
* **value** - arn:aws:eks:region-code:111122223333:cluster/my-cluster

These parameters instruct the Aembit Edge Components to configure the Agent Proxy containers with an environment variable named `KUBERNETES_PROVIDER_ID` with the value indicated.

> **Note**
>
> This Wiz integration supports Agent Proxy versions 1.8.1203 and higher.

### Create a new Wiz -> Aembit integration

Once you have set up your Wiz API client and are ready to integrate Wiz with your Aembit Tenant, follow the steps listed below.

1. Sign into your Aembit Tenant.

2. Click on the **Access Conditions** page in the left sidebar. You should see a list of existing Access Conditions. In this example, there are no existing access conditions.

![Access Conditions page](https://docs.aembit.io/_astro/access_conditions_blank.Dr-PNxRw_ZtpIbO.webp)

3. At the top of the page, in the *Access Conditions* tab, select **Integrations**, and then select **New**. An Integrations page appears showing the types of integrations you can create. Currently, there are two integration types available: Wiz or CrowdStrike.

![Main Integrations Page](https://docs.aembit.io/_astro/integrations_page.SytoyDqi_hPxwP.webp)

4. Select the **Wiz Integration API** tile. You will see the *Wiz Integration* page.

![Wiz Integration Page](https://docs.aembit.io/_astro/wiz_integration_page.InkgabQz_Z266jN3.webp)

5. On this page, enter the following values from your Wiz API client (these are the values you saved earlier when creating your Wiz API client).

* **Name** - The name of the Integration you want to create.

* **Description (optional)** - An optional text description for the Integration.

* **Endpoint** - The *Base URL* value taken from the values you copied when creating your Wiz API key.

* **Sync Frequency** - The amount of time (interval) between synchronization attempts. This value can be between 5 minutes up to 1 hour.

* **Oauth Token Configuration information** -

  * **Token Endpoint** - The endpoint for your token.
  * **Client ID** - The *Client ID* value.
  * **Client Secret** - The *Client Secret* value.
  * **Audience** - This value should be set to `wiz-api` as recommended by the Wiz Integration API documentation.

7. Click the **Save** button when finished. Your Integration is saved and will then appear on the Integrations page.

> **Note**
>
> After the next sync attempt, the status will be updated to show success/failure details.
