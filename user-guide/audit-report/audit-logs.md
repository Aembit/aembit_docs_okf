---
type: how-to
title: "How to review Audit Logs"
description: "How to review Audit Log information in the Reporting Dashboard"
resource: https://docs.aembit.io/user-guide/audit-report/audit-logs/
interface: web-ui
tags: [audit-report]
timestamp: 2025-06-03T16:56:07-07:00
type_inferred: true
---

# How to review Audit Logs

Your Aembit Tenant includes the ability for you to review detailed audit log information so you can troubleshoot any issues encountered in your environment. Having this data readily available can assist you in diagnosing any issues that may arise, while also providing you with detailed information about these events.

## Retrieving audit log data

[Section titled “Retrieving audit log data”](#retrieving-audit-log-data)

To retrieve event information from audit logs, perform the following steps:

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

3. At the top, select **Reporting ☰ Audit Logs**.

   Aembit displays the **Audit Logs** page with a list of existing Audit Logs.

   ![Audit Logs Main Page](https://docs.aembit.io/_astro/reporting-audit-logs-main-page.BMM26s9f_Z1ubIAx.webp)

4. By default Aembit displays all logs. You can filter the results to your liking using the following:

   * **Timespan** - The period of time you would like to have audit logs data displayed.

     Default - **30 Days**

     Options - `1 Day`, `15 Days`, `30 Days`, `3 Months`, `6 Months`, `1 Year`, or `All`

   * **Category** - The type of event information you want displayed.

     Default - **All**

     Options - `AccessConditions`, `AccessPolicies`, `AgentControllers`, `Agents`, `Authentication`, `CredentialProvider`, `CredentialProviderIntegrations`, `DiscoveryIntegration`, `GlobalPolicyCompliance`, `IdentityProviders`, `Integrations`, `LogStreams`, `PkiSettings`, `ResourceSets`, `Roles`, `Routing`, `SignOnPolicies`, `StandaloneCertificateAuthorities`, `Tenant`, `TrustProvider`, `Users`, `Workloads`.

   * **Severity** - The level of importance of the event.

     Default - **All**

     Options - `Alert`, `Warn`, `Info`

5. Once you have selected your filtering options, Aembit displays the audit log information based on your selections in the table.

### Audit logs reporting example

[Section titled “Audit logs reporting example”](#audit-logs-reporting-example)

If you would like to review detailed audit log information for an event, select the event. This expands the window for that event, enabling you to see both a summary of the event (on the left side of the information panel), and detailed JSON output (on the right side of the information panel).

The following example shows audit log information for an event where Trust Provider attestation failed.

![Audit Logs Reporting Example](https://docs.aembit.io/_astro/reporting-audit-log-attestation.CcuzlOH4_ZLBwEd.webp)

In the left side of the information panel, you see a summary of the event information displayed, including:

* **Timestamp** - The time the event was recorded.
* **Actor** - The entity responsible for the request.
* **Category** - The category of the event.
* **Activity** - The type of request being made.
* **Target** - The identifier of the entity that you are running the activity against. For example, if you are editing a Credential Provider, the target is the name of the Credential Provider.
* **Result** - The result of the event.
* **Client IP** - The IP address of the user or workload that executed the action that is recorded by the audit log.
* **Browser** - The browser used by the client.
* **Operating System** - The operating system used by the client.
* **User Agent** - The User-Agent HTTP header included in the API request that generated the audit log activity.

In the right side of the information panel, you see the more granular, detailed information, including:

* **ExternalID** - The external ID of the audit log.
* **Resource Set ID** - The Resource Set ID of the entity affected by the audit log generating activity.
* **Category** - The category of the event in the audit log.
* **Actor** - The entity responsible for the request.
* **Activity** - The type of request being made.
* **Target** - The target entity of the action represented by the audit log record.
* **Client** - The metadata for the Client (e.g. browser) environment.
* **Outcome** - The verdict of the request.
* **Trust Provider** - The Trust Provider used in the request. Note that this value is only applicable for Trust Provider attestation based authentication (e.g. Agent Controller attested authentication or Proxyless authentication).
* **Severity** - The severity of the event.
* **Created At** - The time the request was made.
