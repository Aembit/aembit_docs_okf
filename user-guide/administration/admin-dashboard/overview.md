---
type: reference
title: "Admin dashboard overview"
description: "This page describes the different views and dashboards on the Aembit Admin Dashboard"
resource: https://docs.aembit.io/user-guide/administration/admin-dashboard/
interface: web-ui
tags: ["admin-dashboard", "administration"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Admin dashboard overview

When logging into your Aembit Tenant, you are immediately shown the Admin dashboard, which displays detailed workload and operational information. Whether you want to see the number of Client Workloads requesting access to Server Workloads over the last 24 hours, or view the number of credentials requests recorded over a 24-hour period for a specific usage type, the Admin Dashboard provides you quick access to these views so you can glean insight into your Aembit environment’s performance.

## The Admin Dashboard

To view the Admin Dashboard.

1. Log into your Aembit Tenant with your user credentials.

2. Once you are logged in, you are directed to the Admin Dashboard, where you see data displayed in various panels.

![Admin Dashboard Main Page](https://docs.aembit.io/_astro/admin_dashboard_main.CqIVsxee_2jgVgn.webp)

You should see the following tiles:

* Summary
* Workload Events
* Client Workloads (Managed)
* Server Workloads (Managed)
* Credentials (Usage By Type)
* Workload Connections (Managed)
* Access Conditions (Most Access Conditions Failures)

### Summary + Workload Events

#### Summary

The **Summary** panel displays the number of configured workloads and entities in your Aembit environment, including the number of entities that are currently inactive.

* Client Workloads
* Trust Providers
* Access Conditions
* Credential Providers
* Server Workloads

![Admin Dashboard - Summary](https://docs.aembit.io/_astro/admin-dashboard-summary.Co-3Rzpc_Z19rMC2.webp)

:::note When you click on one of these panels, the **Summary** tab opens the dashboard page for that resource with a list of existing configurations. :::

#### Workload Events

The **Workload Events** panel displays the number of Workload Events recorded over the last 6 hours. This historical data can be very useful in measuring how many workload events occurred over a set period of time. With this data, you can optimize your Aembit environment; this includes the workload event severity so users can quickly identify connectivity issues.

![Admin Dashboard - Workload Events](https://docs.aembit.io/_astro/admin-dashboard-summary.Co-3Rzpc_Z19rMC2.webp)

If you select the **Refresh** button, you can refresh the results to view newly received events, enabling you to view the latest event records and make any necessary changes if needed to ensure your Aembit environment is operating efficiently.

### Client Workloads (Managed)

The **Client Workloads (Managed)** panel displays the number of managed Client Workloads that attempted to access Server Workloads over the last 24 hours, sorted from top to bottom based on the number of Client Workload connections. This information can be helpful in determining which Client Workloads are accessing Server Workloads in your Aembit environment and identifying the most active Client Workloads.

![Managed Client Workloads](https://docs.aembit.io/_astro/admin-dashboard-managed-client-workload-tile.Ca23GVZA_2tG2Yg.webp)

### Server Workloads (Managed)

The **Server Workloads (Managed)** panel displays the number of managed Server Workload connections that were recorded over the last 24 hours, sorted from top to bottom based on the number of requests received for the Server Workload. This information can be helpful in determining which Server Workloads are being accessed in your Aembit environment and identifying the most active Server Workloads.

![Managed Server Workloads](https://docs.aembit.io/_astro/admin-dashboard-managed-server-workload-tile.DHr_cTHV_wk3xE.webp)

### Credential (Usage By Type)

The **Credential (Usage By Type)** panel displays a pie chart showing the total number of credential types that were issued in the past 24 hours. This information can be helpful in determining which credential types are most frequently being used. Aembit encourages the use of short-lived credentials wherever possible. By identifying the usage level of different credential types, this chart can be helpful when transitioning from long-lived to short-lived credentials.

![Credential Provider Usage By Type](https://docs.aembit.io/_astro/admin-dashboard-credential-provider-usage-by-type-1--tile.CaiObTuE_ZT7Q5l.webp)

### Workload Connections (Managed) / Application Protocol

The **Workload Connections** panel displays the number of managed Workload Connections that were recorded over the last 24 hours, sorted from top to bottom based on the type of application protocol used in the request.

![Workload Connections By Application Protocol](https://docs.aembit.io/_astro/admin-dashboard-app-protocol-pie-tile-1.DVHiFAMZ_77PgI.webp)

### Access Policies (Most Access Condition Failures)

The **Access Policies (Most Access Condition Failures)** panel displays the number of Access Condition failures based on Access Policies. In this chart, you can see that Aembit was able to identify Client Workloads and Server Workloads on Access Policies, but the Access Condition fails and these workloads can therefore not be attested, enabling you to identify how many attestations are failing because of Access Conditions.

In the example shown below, notice that for the VM1 - Production Instance, the most Access Condition failures occurred for Microsoft Graph API and Redshift DB - Ohio.

![Access Policy Failures](https://docs.aembit.io/_astro/admin-dashboard-access-policies-most-access%20condition-failures.C6B3w0xM_ZNyaG2.webp)
