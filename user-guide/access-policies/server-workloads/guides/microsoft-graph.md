---
type: how-to
title: "Microsoft Graph"
description: "This page describes how to configure Aembit to work with the Microsoft Graph Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/microsoft-graph/
interface: web-ui
tags: ["cloud", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Microsoft Graph


[Microsoft Graph API](https://developer.microsoft.com/en-us/graph) is a comprehensive cloud-based service that empowers developers to build applications that integrate seamlessly with Microsoft 365. This service serves as a unified endpoint to access various Microsoft 365 services and data, offering a range of functionalities for communication, collaboration, and productivity.

Below you can find the Aembit configuration required to work with the Microsoft service as a Server Workload using the Microsoft Graph REST API.

## Prerequisites

Before proceeding with the configuration, ensure you have the following:

* Microsoft Azure tenant.
* A registered and consent-granted application on Microsoft Entra ID (previously Azure Active Directory). If you haven’t set up an app yet, follow the steps in the next section.

### Microsoft Entra ID (Azure Active Directory) App Registration

1. Log in to the [Microsoft Azure Portal](https://portal.azure.com/#home).

2. Navigate to **Microsoft Entra ID** (Azure Active Directory).

3. On the left panel, click on **App registrations**, and then from the right part, click on **New registration** in the ribbon list.

4. Choose a user-friendly name, select the **Accounts in this organizational directory only** option, and then click **Register**. Your application is now registered with Microsoft Entra ID (Azure Active Directory).

![Register an application](https://docs.aembit.io/_astro/microsoft_register_app.CuDBAixI_1Bp87i.webp)

5. To set API Permissions, on the left panel, click on **API Permissions**, and then on the right part, click on **Add a permission**. In the opened dialog, click on **Microsoft Graph** and then click **Application permissions**.

> **Note**
>
> The current configuration with Microsoft Graph ***only*** works for the Application permission type. For more details on permissions and types, please refer to the [official Microsoft article](https://learn.microsoft.com/en-us/graph/permissions-overview?tabs=http).

![Set API Permissions](https://docs.aembit.io/_astro/microsoft_set_permission.B2RHoWDa_1I9eH6.webp)

6. Select the permissions your workload needs. Since there are many permissions to choose from, it may help to search for the ones you want. Then, click on **Add permissions**.

7. Under Configured Permissions, click on **Grant admin consent for…**, and then click **Yes**.

![Grant Admin Consent](https://docs.aembit.io/_astro/microsoft_grant_consent.DKd1urKK_29QUdE.webp)

Before an app accesses your organization’s data, you need to grant specific permissions. The level of access depends on the permissions. In Microsoft Entra ID (Azure Active Directory), Application Administrator, Cloud Application Administrator, and Global Administrator are [built-in roles](https://learn.microsoft.com/en-us/entra/identity/role-based-access-control/permissions-reference) with the ability to manage admin consent request policies.

If the button is disabled for you, please contact your Administrator. Note that only users with the appropriate privileges can perform this step. For more information on granting tenant-wide admin consent, refer to the [official Microsoft article](https://learn.microsoft.com/en-us/entra/identity/enterprise-apps/grant-admin-consent?pivots=portal).

## Server Workload Configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the service endpoint:

* **Host** - `graph.microsoft.com`
* **Application Protocol** - HTTP
* **Port** - 80
* **Forward to Port** - 443 with TLS
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Bearer

## Credential Provider Configuration

1. Log in to the [Microsoft Azure Portal](https://portal.azure.com/#home).

2. Navigate to **Microsoft Entra ID** (Azure Active Directory) and on the left panel click on **App registrations**.

3. Select your application.

4. In the Overview section, copy both the **Application (client) ID** and the **Directory (tenant) ID**. Store them for later use in the tenant configuration.

![Overview | Copy Client ID and Tenant ID](https://docs.aembit.io/_astro/microsoft_overview_workload.QKXGf4WJ_1C2Pje.webp)

5. Under Manage, navigate to **Certificates & secrets**. In the Client Secrets tab, if there is no existing secret, please create a new secret and make sure to save it immediately after creation. If there is an existing one, please provide the stored secret in the following steps.

![Copy Client Secret](https://docs.aembit.io/_astro/microsoft_client_secret.CWemjeOd_rv8JX.webp)

6. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
* **Token endpoint** - ht​tps\://login.microsoftonline.com/**Your-Tenant-Id**/oauth2/v2.0/token
* **Client ID** - Provide the client ID copied from Azure.
* **Client Secret** - Provide the client secret copied from Azure.
* **Scopes** - <https://graph.microsoft.com/.default>

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an Access Policy for a Client Workload to access the Microsoft Server Workload. Assign the newly created Credential Provider to this Access Policy.

## Related

**Compatible credential providers**

* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
