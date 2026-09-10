---
type: how-to
title: "Box"
description: "This page describes how to configure Aembit to work with the Box Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/box/
interface: web-ui
tags: ["productivity", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Box


[Box](https://www.box.com/en-gb/home) is a cloud content management and file sharing service designed to help businesses securely store, manage, and share files online. The Box API provides developers with tools to integrate Box’s content management features into their own applications, enabling efficient collaboration and secure file handling.

Below you can find the Aembit configuration required to work with the Box service as a Server Workload using the Box API.

## Prerequisites

Before proceeding with the configuration, ensure you have the following:

* Box tenant.
* A custom authorized application using Server Authentication in the Box tenant. If you have not created an app yet, follow the steps outlined in the next section or refer to the [official Box Developer documentation](https://developer.box.com/guides/authentication/client-credentials/) for more detailed instructions.
* 2FA enabled on your Box tenant to view and copy the application’s client secret.

### Create New App In Box

1. Log in to the [Box Developer Console](https://app.box.com/developers/console).

2. Navigate to the left pane, select **My Apps**, and then click on **Create New App** in the top-right corner.

![Box Create New App](https://docs.aembit.io/_astro/box_create_app.BAKFoYRy_1VLRWo.webp)

3. Choose **Custom App**. A pop-up window will appear. Fill in the name and optional description field, choose the purpose, and then click **Next** to proceed.

4. Select **Server Authentication (Client Credentials Grant)** as the authentication method and click **Create App**.

5. Before the application can be used, a Box Admin must authorize it within the Box Admin Console. Navigate to the **Authorization** tab and click **Review and Submit** to send the request. A pop-up window will appear. Fill in the description field and click **Submit** to send. After your admin [authorizes the app](box.md#authorize-app-as-an-admin), the Authorization Status and Enablement Status should both be green.

![Box Authorized App](https://docs.aembit.io/_astro/box_authorized_app.CfJFluSn_fl9wm.webp)

6. Go back to the **Configuration** tab and scroll down to the **Application Scopes** section. Choose the scopes that best suit your project needs and click **Save Changes** in the top-right corner.

### Authorize App As an Admin

1. Navigate to the [Admin Console](https://app.box.com/master).

2. In the left panel, click on **Apps**, and then in the right panel, click on **Custom Apps Manager** in the ribbon list to view a list of your Server Authentication Apps.

3. Click the 3-dot-icon of the app that requires authorization.

4. Choose **Authorize App** from the drop-down menu.

![Box Authorize App as Admin](https://docs.aembit.io/_astro/box_authorize_app_as_admin.BReH2jGt_Z1S6O0y.webp)

5. A pop-up window will appear. Click **Authorize** to proceed.

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `api.box.com`
* **Application Protocol** - HTTP
* **Port** - 443 with TLS
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

1. Log in to the [Box Developer Console](https://app.box.com/developers/console).

2. Navigate to the left pane, select **My Apps**, and then click on the name of the app to view details.

3. In the General Settings tab, copy the **Enterprise ID**.

![General Settings | Copy Enterprise ID](https://docs.aembit.io/_astro/box_copy_enterprise_id.CQWU6u64_Z1Hedoy.webp)

4. In the Configuration tab, scroll down to the **OAuth 2.0 Credentials** section. Click **Fetch Client Secret** and then copy both the **Client ID** and **Client Secret**. Keep these details stored for later use in the tenant configuration.

![Configuration | Copy Client ID and Tenant ID](https://docs.aembit.io/_astro/box_copy_client_id_secret.DBEiM2Nj_Z1KL8ak.webp)

5. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.

* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)

* **Token endpoint** - `https://api.box.com/oauth2/token`

* **Client ID** - Provide the client ID copied from Box.

* **Client Secret** - Provide the client secret copied from Box.

* **Scopes** - You can leave this field **empty**, as Box will default to your selected scopes on the Developer Console, or specify the scopes, such as `root_readonly`. For more detailed information for scopes, you can refer to the [official Box Developer documentation](https://developer.box.com/guides/api-calls/permissions-and-errors/scopes/#scopes-oauth-2-authorization).

* **Credential Style** - POST Body

**Additional Parameters**

> **Note**
>
> The following parameters are used to authenticate as the application’s **Service Account**. To authenticate as a **Managed User**, refer to the [official Box Developer documentation](https://developer.box.com/guides/authentication/client-credentials/) for additional configuration steps. For security purposes, we recommend using the service account option and collaborating your service account on just the content it needs to access.

* **Name** - box\_subject\_type

  * **Value** - enterprise

* **Name** - box\_subject\_id

  * **Value** - Provide the enterprise ID copied from Box.

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Box Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the Box Server Workload.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
