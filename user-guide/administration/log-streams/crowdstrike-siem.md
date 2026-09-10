---
type: how-to
title: "How to stream Aembit events to CrowdStrike Next-Gen SIEM"
description: "How to create a new a Log Stream for CrowdStrike Next-Gen SIEM"
resource: https://docs.aembit.io/user-guide/administration/log-streams/crowdstrike-siem/
interface: web-ui
tags: ["log-stream", "administration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How to stream Aembit events to CrowdStrike Next-Gen SIEM

Aembit’s Log Stream to CrowdStrike Next-Gen Security Information and Event Management (SIEM) feature enables rapid streaming of Aembit Edge event logs and audit logs directly to CrowdStrike. This integration uses the HTTP Event Collector (HEC) protocol to deliver comprehensive security data, enhancing threat detection capabilities, improving incident management, and streamlining compliance monitoring for your organization.

## Prerequisites

Before you can stream Aembit events to CrowdStrike Next-Gen SIEM, you must have an HTTP Event Collector (HEC) set up in your CrowdStrike environment with the following attributes:

* A **Data Connection** with the following:

  * **Connector Name** - `HEC / HTTP Event Connector`
  * **Data Source** -
  * **Data Type** - JSON

Once you’ve created the Data Connection, click the \*\*Generate

Use your HEC **Connector name** and **API key** values in the CrowdStrike Next-Gen SIEM Log Stream configuration in your Aembit Tenant.

To configure an **HEC/HTTP Event Data Connector** in CrowdStrike, see the [HTTP Event Collector Guide](https://falcon.us-2.crowdstrike.com/documentation/page/bdded008/hec-http-event-connector-guide) in CrowdStrike’s official docs.

## Create a CrowdStrike Next-Gen SIEM Log Stream

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Log Streams**.

   Aembit displays the **Log Streams** page with a list of existing Log Streams.

4. Click **+ New**, which displays the Log Streams pop out menu.

5. Fill out the following fields:

   * **Name** - Enter a name for the Log Stream.

   * **Description** - Enter an optional description for the Log Stream.

   * **Event Type** - Select the type of event you want to stream to your CrowdStrike Next-Gen SIEM. Choose from: `Access Authorization Events`, `Audit Logs`, and `Workload Events`

6. Select **CrowdStrike Next-Gen SIEM using Http Event Collector** as the **Destination Type**.

7. Fill out the revealed fields:

   * **CrowdStrike Host/Port** - Enter the hostname or IP address and port of your CrowdStrike host.

     Make sure to **exclude the protocol** and **include the port number**. For example: `2c87fd0df4c44ec69e06bc7f2d754faa.ingest.us-2.crowdstrike.com:443`

     * (Optional) Check **TLS** to enable TLS communication between your CrowdStrike host and Aembit.

   * (Optional) **TLS Verification** - Select the desired option to enable TLS verification.

   * **API Key** - Enter the **API Key** from your CrowdStrike HEC.

   * **Source Name** - Enter the **Connector Name** from your CrowdStrike HEC.

8. Click **Save**.

   Aembit displays the **Log Stream** on the **Log Streams** page.

Once you save your Log Stream, you can view its details by selecting it in the list of Log Streams to see something similar to the following screenshot:

![Completed CrowdStrike Next-Gen SIEM Log Stream](https://docs.aembit.io/_astro/log-stream-crowdstrike-siem-complete.BwCDQRT1_Z2qD23n.webp)

## Monitor logs in CrowdStrike SIEM

After configuration, you can view logs that Aembit generates from the event type you selected in the CrowdStrike Next-Gen SIEM UI by doing the following:

1. Log into your CrowdStrike Next-Gen SIEM.

2. Go to **Data connections**.

3. In the list of **Connections**, select **Show events** from the **Actions** menu for the connection you created in the prerequisites section.

   CrowdStrike displays the **Search** page pre-populated your connections details with a list of events in the **Results** pane.

You should see results similar to the following on all logs that Aembit streams to CrowdStrike Next-Gen SIEM:

```text
#repo: 3pi_auto_raptor_174204601818S
#repo.cid: 599f927991a44b3Gae1b7fcf0acd2911
#type: json
@dataConnectionID: 6qb4ef044ccc646bfb0c38617cc3f1ee7
@id: vpQ8NosDWpukc2HZ4ELXv9G9_2_3_1742066422
@ingestTimestamp: 1742066500390
@rowString: {"timestamp":"2025-03-15T19:20:22.183751Z","source":"http.AembitDev","tenant":"3qb5d","meta":{"clientIP":"34.232.129.136","timestamp":"2025-
03-15T00:00:21.183751Z","eventType":"access.request","eventId":"d34d67b8-e22b-436e-bf35-489fe8089e56","resourceSetId":"ffffffff-ffff-ffff-ffff-ffffffffffff","contex
tId":"9fae3f4c-f16a-452a-99c5-ea095fc2a8bc","severity":"Info","clientRequest":{"version":"1.0.0","network":{"sourceIP":"127.0.0.1","sourcePort":46717,"transpor
tProtocol":"TCP"},"environment":{"dembit.clientId":"f86ef924-363636-4be2-b992-b313c54968e"},"network":{"sourceIP":"127.0.0.1"},"dembit":{"clientId":"f86ef924-363
6-4be2-a992-b313c54968e"}}}
@source: PlotFormEvents
@sourcetype: json
@timestamp: 1742066422183
@timestamp.nanos: 751000
@timezone: Z
clientRequest.network.proxyPort: 0
clientRequest.network.sourceIP: 127.0.0.1
clientRequest.network.sourcePort: 46717
clientRequest.network.targetHost: igm.googleapis.com
clientRequest.network.targetPort: 443
clientRequest.network.transportProtocol: TCP
clientRequest.version: 1.0.0
environment.dembit.clientId: f86ef924-3636-4be2-a992-b313c54968e
environment.network.sourceIP: 127.0.0.1
meta.clientIP: 256.256.256.256
meta.contextId: 9fae3f4c-f16a-452a-99c5-ea095fc2a4ert
meta.eventId: d34d67b8-e22b-436e-bf35-489fe802a4e54
meta.eventType: access.request
meta.resourceSetId: ffffffff-ffff-ffff-ffff-ffffffffffff
meta.severity: Info
meta.timestamp: 2025-03-15T00:00:21.183751Z
source: http.AembitDev
```

## Failure notifications

If your Aembit account has write privileges for Log Streams, Aembit automatically sends you and email notification when Log Stream transactions consistently fail.
