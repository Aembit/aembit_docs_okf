---
type: how-to
title: "CrowdStrike Integration"
description: "This page describes how to integrate CrowdStrike with Aembit."
resource: https://docs.aembit.io/user-guide/access-policies/access-conditions/integrations/crowdstrike/
interface: web-ui
tags: [integration, access-condition, access-policy]
timestamp: 2025-05-22T22:55:26-07:00
type_inferred: true
---

# CrowdStrike Integration

Note

The CrowdStrike Integration feature is a paid feature. To enable CrowdStrike integration, please contact Aembit by completing the [Contact Us form](https://aembit.io/contact/).


CrowdStrike is a cybersecurity platform that provides cloud workload and endpoint security, threat intelligence, and cyberattack response services to businesses and enterprises.

While Aembit provides workload identity and access management, integrating with a 3rd party service, such as CrowdStrike, enables businesses to prevent Server Workload access from Client Workloads that do not meet an expected state. If the Client Workload environment is not in this state, workload access will not be authorized.

Note

A specific expected state is defined as a configured set of conditions as defined in one or more Aembit access condition rules. For example, in CrowdStrike, this may be when an agent is operating in Reduced Functionality Mode.

## CrowdStrike Falcon Sensor

[Section titled “CrowdStrike Falcon Sensor”](#crowdstrike-falcon-sensor)

The CrowdStrike Falcon Sensor is a lightweight, real-time, threat intelligence application installed on client endpoints that reviews processes and programs to detect suspicious activity or anomalies.

To integrate CrowdStrike Falcon with Aembit Cloud, you will need to:

* create a new API key
* create a new CrowdStrike integration

### Create a new CrowdStrike OAuth2 API Client

[Section titled “Create a new CrowdStrike OAuth2 API Client”](#create-a-new-crowdstrike-oauth2-api-client)

To create a new CrowdStrike OAuth2 API Client:

1. Generate an API key from the CrowdStrike website (for example `https://falcon.us-2.crowdstrike.com/api-clients-and-keys/clients` ). Note that URLs may change over time, therefore, you should always use the latest URLs listed on the CrowdStrike site.

2. In the Create API Client dialog, enter the following information:

* Name

* Description (optional)

![Create a new CrowdStrike OAuth2 API Client](https://docs.aembit.io/_astro/create_api_key.ByDxIOgd_1jarOx.webp)

3. Click on the **Hosts** checkbox in the Read column to enable the Hosts -> Read permission.

4. Click the **Create** button to generate your new API client.

5. You will see a dialog appear with the following information:

* Client ID
* Secret
* Base URL

Note

It is important that you copy this information and store it in a safe location. You will need this information later when you configure your CrowdStrike integration in your Aembit Tenant.

![API Client Created](https://docs.aembit.io/_astro/api_client_created.B99vvPC1_ZjspiW.webp)

6. Once you have copied the API client information, click **Done** to close the dialog.

Now that you have created your new API client, you will need to add this information to your Aembit Tenant by following the steps described below.

### Create a new CrowdStrike -> Aembit integration

[Section titled “Create a new CrowdStrike -> Aembit integration”](#create-a-new-crowdstrike---aembit-integration)

To integrate CrowdStrike with your Aembit Tenant:

1. Sign into your Aembit Tenant.

2. Click on the **Access Conditions** page in the left sidebar. You should see a list of existing Access Conditions. In this example, there are no existing access conditions.

![Access Conditions page](https://docs.aembit.io/_astro/access_conditions_blank.Dr-PNxRw_ZtpIbO.webp)

3. Click on the **Create an Integration** button. The main Integrations page is displayed.

![Integrations Page](https://docs.aembit.io/_astro/integrations_page.SytoyDqi_hPxwP.webp)

4. Select the **CrowdStrike** Integration tile.

5. On the Aembit Integrations page, configure your CrowdStrike Integration by entering the values you just copied in the fields below.

* **Name** - The name of the Integration you want to create.

* **Description (optional)** - An optional text description for the Integration.

* **Endpoint** - The *Base URL* value taken from the values you copied when generating your CrowdStrike API key.

* **Oauth Token Configuration information** -

  * **Token Endpoint** - The endpoint for your token. The value entered should be: *BaseURL + “/oauth2/token”*
  * **Client ID** - The *Client ID* value taken from the values you copied when generating your CrowdStrike API key.
  * **Client Secret** - The *Client Secret* value taken from the values you copied when generating your CrowdStrike API key.

Note

You can retrieve the correct BaseURL by referring to your [API Client page](https://falcon.us-2.crowdstrike.com/api-clients-and-keys/clients), and additionally, in the [BaseURLs](https://falcon.us-2.crowdstrike.com/documentation/page/a2a7fc0e/crowdstrike-oauth2-based-apis#k9578c40) section of the CrowdStrike API documentation.

![Integration Example](https://docs.aembit.io/_astro/integration_example.DaWK2pij_Z17RjKO.webp)

7. Click the **Save** button when finished. Your CrowdStrike Integration is saved and will then appear on the Integrations page.
