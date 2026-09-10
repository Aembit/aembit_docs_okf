---
type: explanation
title: "Access Authorization Events"
description: "This page describes how users can review access authorization event information in Aembit Reporting."
resource: https://docs.aembit.io/user-guide/audit-report/access-authorization-events/
tags: ["audit-report"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Access Authorization Events

An access authorization event is an event that Aembit generates that occurs whenever an Edge Component requests access to a Server Workload. When Aembit receives an access request, the generated events include detailed information, providing a granular view of the processing steps to evaluate the request against an existing Access Policy. Once Aembit Cloud processes the request and completes the evaluation, it generates a result that specifies whether it granted or denied access (success or failure).

Having the ability to view information about these access authorization events enables you to not only troubleshoot issues, but also have a historical records of these events. You may also use these logs to perform threat detection analysis to ensure malicious actors and workloads don’t gain access to your resources.

> **MCP access authorization events**
>
> MCP access authorization events include `authorizationChain`, which contains an ordered list of context IDs that identify the authorization decisions behind a request.
>
> The same field also appears on the corresponding [MCP Workload Events](workload-events/overview.md), so you can take any context ID from the chain to trace a request across both surfaces. See [Trace a request with the authorization chain](../troubleshooting/mcp-ai-iam.md).
>
> This field requires version 1.32 or later. Events from earlier components don’t include it.

## Event types

The four different types of access authorization events that you may view in the Aembit Reporting dashboard include:

* Access Request
* Access Authorization
* Access Discovery
* Access Credential

## Access request events

An `access.request` event captures the request and associated metadata.

The following example shows an `access.request` event type.

```json
{
  "meta": {
    "clientIP": "1.2.3.4",
    "timestamp": "2024-09-14T20:29:11.0689334Z",
    "eventType": "access.request",
    "eventId": "5b788a92-accd-49a1-851f-171f7c20d355",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff",
    "contextId": "4e876ace-d1b0-4095-ac22-f9c0fb7e676a",
    "severity": "Info"
  },
  "clientRequest": {
    "version": "1.0.0",
    "network": {
      "sourceIP": "10.0.0.15",
      "sourcePort": 53134,
      "transportProtocol": "TCP",
      "proxyPort": 8080,
      "targetHost": "server.domain.com",
      "targetPort": 80
    }
  }
}
```

## Access authorization events

In an `access.authorization` event, you can view detailed information about the steps Aembit Cloud Control Plane undertakes to evaluate an Access Policy. An access authorization event includes event metadata and the outcome of the evaluation. It also includes details about the identified Client Workload, Server Workload, Access Policy, Trust Providers, Access Conditions, and Credential Provider.

The following example shows the type of data you should expect to see in an access authorization event.

```json
{
  "meta": {
    "clientIP": "1.2.3.4",
    "timestamp": "2024-09-14T20:29:11.0689334Z",
    "eventType": "access.authorization",
    "eventId": "5b788a92-accd-49a1-851f-171f7c20d355",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff",
    "contextId": "4e876ace-d1b0-4095-ac22-f9c0fb7e676a",
    "severity": "Info"
  },
  "outcome": {
    "result": "Unauthorized",
    "reason": "Attestation failed"
  },
  "clientWorkload": {
    "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
    "name": "Test Client",
    "result": "Identified",
  },
  "serverWorkload": {
    "id": "49183921-55ab-4856-a8fc-a032af695e0d",
    "name": "Test Server",
    "result": "Identified",
  },
  "accessPolicy": {
      "id": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
      "name": "Test Access Policy",
      "result": "Identified"
  },
  "trustProviders": [{
      "id": "24462228-14c1-41a4-8b23-9be789b48452",
      "name": "Kerberos",
      "result": "Attested"
    },{
      "id": "c0bd6c06-71ce-4a87-b03c-4c64cb311896",
      "name": "AWS Production",
      "result": "Unauthorized",
      "reason": "InvalidSignature"
    },{
      "id": "5f0c2962-2af4-4b5f-97c0-9046b37198a9",
      "name": "Kubernetes",
      "result": "Unauthorized",
      "reason": "MatchRuleFailed",
      "attribute": "serviceNameUID",
      "expectedValue": "foo",
      "actualValue": "bar",
  }],
  "accessConditions": [],
  "credentialProvider": {
    "id": "bb7927f8-060c-4486-9a5e-bcbe1efc53d6",
    "name": "Production PostgreSQL",
    "result": "Identified",
    "maxAge": 60,
  }
}
```

### Authorization failure

If an authorization request fails during the check, the `trustProviders` and/or `accessConditions` elements include a `reason` property value that notifies you a failure occurred and explains why. By providing you a reason for the failure, you can then use this information to diagnose and troubleshoot the issue.

A failure can return different types of `reason` values, including:

* **NoDataFound** - Attestation didn’t succeed because the necessary data wasn’t available.
* **InvalidSignature** - The cryptographic verification check failed.
* **MatchRuleFailed** - The match rules for the Trust Provider weren’t satisfied.
* **ConditionFailed** - The Access Condition check failed.

In the preceding example, notice that the `Trust Providers` check failed.

For the Trust Provider ID `5f0c2962-2af4-4b5f-97c0-9046b37198a9` in this example, the reason specified in the JSON response is:

* `MatchRuleFailed`

With this information, you can determine that the `Kubernetes` Trust Provider failed its match rules: the check was looking for the `serviceNameUID` attribute with the expected value `foo` but found `bar`. The `AWS Production` Trust Provider in the same response failed its cryptographic verification check (`InvalidSignature`). Now that you know why the failures occurred, you can troubleshoot the issue.

### Content Security

When an Access Policy includes a Content Security Provider, the `access.authorization` event lists the provider that Aembit identified for the policy in the `contentSecurity` element. The element records the provider’s `id` and `name` with a `result` of `Identified`, following the same pattern as the `trustProviders` and `accessConditions` elements. When a policy has no Content Security Provider, `contentSecurity` is an empty array.

Example `contentSecurity` element in an `access.authorization` event

```json
{
  "accessPolicy": {
    "id": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
    "name": "MCP Production Policy",
    "result": "Identified"
  },
  "accessConditions": [],
  "contentSecurity": [{
    "id": "9f3b1c07-5d2a-4e18-b6c4-2ae7f0d91b35",
    "name": "CrowdStrike AIDR Inspection",
    "result": "Identified"
  }]
}
```

The element tells you which Content Security Provider applied to the request the policy authorized. The decisions themselves appear in [MCP workload events](workload-events/supported-protocols.md#content-security), not in Access Authorization Events.

## Access discovery events

An `access.discovery` event captures the Client Workloads and Server Workloads that Aembit Cloud considered while evaluating an access request. It’s most useful for diagnosing requests that fail because no workload or policy matched, or because multiple workloads matched a single request.

Aembit emits Access Discovery events alongside the standard `access.request` and `access.authorization` events when discovery applies. For example, during Model Context Protocol (MCP) Identity Gateway authorization, a single client request can match multiple candidate Server Workloads.

When no workload or policy matches the request, the discovery event has severity `Error` and lists each candidate that Aembit Cloud evaluated along with the reason Aembit Cloud excluded it.

You can filter the Access Authorization Events view by the `access.discovery` event type to review all discovery outcomes over a timespan.

For end-to-end troubleshooting that uses Access Discovery events alongside Workload Events and MCP Authorization Tracing, see [Troubleshoot MCP and AI IAM access](../troubleshooting/mcp-ai-iam.md).

## Access credential events

The `access.credential` event type shows the result of the Edge Controller retrieval attempt of the required credential when requested. If the request was successful, the Edge Controller acquires credentials for the Server Workload via the Credential Provider and the event specifies the result as `Retrieved`.

The following example shows what you should expect to see in an `access.credential` event.

```json
{
  "meta": {
    "clientIP": "1.2.3.4",
    "timestamp": "2024-09-14T20:29:11.0689334Z",
    "eventType": "access.credential",
    "eventId": "5b788a92-accd-49a1-851f-171f7c20d355",
    "resourceSetId": "ffffffff-ffff-ffff-ffff-ffffffffffff",
    "contextId": "4e876ace-d1b0-4095-ac22-f9c0fb7e676a",
    "severity": "Info"
  },
  "outcome": {
    "result": "Authorized",
  },
  "clientWorkload": {
    "id": "7c466803-9dd4-4388-9e45-420c57a0432c",
    "name": "Test Client",
    "result": "Identified",
  },
  "serverWorkload": {
    "id": "49183921-55ab-4856-a8fc-a032af695e0d",
    "name": "Test Server",
    "result": "Identified"
  },
  "accessPolicy": {
      "id": "dd987f8c-34fb-43e2-9d43-89d862e6b7ec",
      "name": "Test Access Policy",
      "result": "Identified"
  },
  "trustProviders": [{
      "id": "49183921-55ab-4856-a8fc-a032af695e0d",
      "name": "Kerberos",
      "result": "Attested"
  }],
  "accessConditions": [],
  "credentialProvider": {
    "type": "oidc-id-token",
    "id": "bb7927f8-060c-4486-9a5e-bcbe1efc53d6",
    "name": "Production PostgreSQL",
    "result": "Retrieved",
    "maxAge": 60,
  }
}
```

The `credentialProvider.type` field identifies the Credential Provider type (for example, `oidc-id-token`, `aws-sts`, `oauth-client-credentials`).

### Credential failure

If a credential request fails during the check, the `credentialProvider` entity includes a `reason` property value that notifies you a failure occurred and explains why. By providing you a reason for the failure, you can then use this information to diagnose and troubleshoot the issue.

A credential request can fail for different reasons, including:

* **Token expired** - The requested token expired. The reason now identifies which token expired (for example, the upstream identity provider’s refresh token, the cached access token, or a Credential Provider-issued credential) and at which step Aembit detected the expiration. This makes it easier to choose between re-authenticating the user, refreshing the credential, or adjusting the Credential Provider configuration.
* **Request failed with BadRequest** - There was a communication error with the credential provider with the HTTP status code.
* **Aembit Internal Error** - There was an internal Aembit error during the credential retrieval.
* **Unknown error** - An unexpected error occurred during credential retrieval, and Aembit support is investigating.

With this information, you can determine the reason for the failure and then troubleshoot the issue.

## Retrieving Access Authorization Event data

To retrieve detailed information about access authorization events, perform the following steps.

1. Log into your Aembit Tenant.

2. Click **Reporting** in the left sidebar.

   By default, Aembit displays the **Access Authorization Events** page with a list of existing Access Authorization Events.

   > **Note**
   >
   > If you don’t see Access Authorization Events, at the top, select **Reporting ☰ Access Authorization Events**.

   ![Reporting Main Dashboard](https://docs.aembit.io/_astro/quickstart_reporting_dashboard.wQyXnMMW_eU8aE.webp)

   The filters at the top of the page enable you to narrow the results displayed:

   * **Timespan** - The period of time you would like to have event data displayed.
   * **Severity** - The level of importance of the event.
   * **Event type** - Filter to a specific event type, including `access.discovery`.

   > **Default filters**
   >
   > When the **Access Authorization Events** page loads, the page loads with default display filters of: **Timespan = 24 Hours**, and **Severity = All**.

3. Select the period of time you would like to view by clicking the **Timespan** dropdown menu. Options are:

   * 1 hour, 3 hours, 6 hours, 12 hours, or 24 hours.

4. Select the severity level of the results you would like to view by clicking the **Severity** dropdown menu. Options are:

   * Error, Warning, Info, or All

5. Select the type of event you want to view by clicking the **Event type** dropdown menu. Apply your filter selections before continuing.

6. Once you have selected your filtering options, the table displays access authorization event information based on these selections.

### Viewing Access Authorization Event data

When you select an access authorization event from the dashboard, you can expand the view to display detailed data for that event. Depending on the event type, the view displays different data. The following sections show examples of the type of data an event may display.

### Access authorization event example

If you would like to review detailed information about an access authorization event, click the event. This expands the view for that event, revealing both a summary of the event with quick links to each entity, and detailed JSON output, including event metadata.

Depending on the type of access authorization event, the information presented in the expanded view is specific to that event type.

For example, the following example shows an event where Trust Provider attestation failed.

#### Trust Provider attestation failure example

In the following example, you can see detailed information about an access authorization event that shows a failure because Aembit couldn’t attest the Trust Provider.

![Trust Provider Failed Attestation Event](https://docs.aembit.io/_astro/reporting-auth-event-failed-trust-provider.BhBnU5cE_Z2lF6jJ.webp)

In the left side of the information panel, you see the following information displayed:

* **Timestamp** - The time when Aembit recorded the event.
* **Client IP** - The client IP address that made the access authorization request. This is typically a network egress IP from your edge environment.
* **Context ID** - ID used to associate the relevant access authorization events together.
* **Event Type** - The type of event that Aembit recorded.
* **Client Workload** - The identified Client Workload ID.
* **Server Workload** - The identified Server Workload ID.

> **Note**
>
> Each of these entities has a quick link, enabling you to go directly to that entity.

In the right side of the information panel, you see the more granular, detailed information displayed about each of these entities, including:

* **Meta** - Metadata associated with the event.
  * Information includes `clientIP`, `timestamp`, `eventType`, `contextId`, `directiveId`, and `severity`.

* **Outcome** - The result of the access authorization request.
  * Options are `Authorized`, `Unauthorized`, or `Error`.

* **Client Workload** - The Client Workload used in the access authorization request.
  * Detailed information includes `id`, `name`, `result`, and `matches`. Note that the `matches` value is optional, and only renders if the request matches multiple Client Workloads.

* **Server Workload** - The Server Workload used in the access authorization request.

  * Detailed information about the Server Workload includes `id`, `name`, `result`, and `matches`.
  * Note that the `matches` value is optional, and only renders if the request matches multiple Server Workloads.

* **Access Policy** - The Access Policy used to evaluate the access authorization request.
  * Information includes `id`, `name`, `result`, and `matches`.

* **Trust Providers** - The Trust Providers assigned to the Access Policy at the time of evaluation.

  * Information in the JSON response includes `id`, `name`, `result`, `attribute`, `expectedValue`, and `actualValue`.
  * The `reason`, `attribute`, `expectedValue` and `actualValue` fields are all optional; however, in the case of Trust Provider attestation failure, you see the `reason` field populated.
  * If the event returns a `reason` value, refer to the [Authorization failure](#authorization-failure) section on this page for more information.

* **Access Conditions** - The Access Conditions assigned to the Access Policy at the time of evaluation.

  * Information in the JSON response includes `id`, `name`, `result`, `attribute`, `expectedValue`, and `actualValue`.
  * The `reason`, `attribute`, `expectedValue` and `actualValue` fields only appear on a failure with the reason `ConditionFailed`.

* **Credential Provider** - The Credential Provider used in the access authorization request.

  * Detailed information includes `id,` `name`, `result`, and `maxAge` values.
  * If a failure occurs during credential retrieval, the event also includes a `reason` value.

> **Note**
>
> If the event returns a `reason` value, refer to the [Credential failure](#credential-failure) section on this page for more information.
