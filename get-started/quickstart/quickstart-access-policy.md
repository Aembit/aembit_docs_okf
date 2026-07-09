---
type: tutorial
title: "Quickstart: Add an Access Policy to the core setup"
description: "Enhancing the Aembit quickstart guide to set up a Trust Provider, Access Conditions, and reporting"
resource: https://docs.aembit.io/get-started/quickstart/quickstart-access-policy/
tags: [quickstart]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Quickstart: Add an Access Policy to the core setup

You’ve completed the [Quickstart guide](quickstart-core.md) and set up your sandbox environment. Now you can enhance your Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md). Add Trust Providers, Access Conditions, and reporting to give you finer control over how you grant access and to gain insights about those interactions.

To build upon your quickstart foundation, you’ll complete practical steps to implement the following features:

* Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../concepts/trust-providers.md) - This verifies workload identities, making sure only authenticated workloads can securely interact with your resources.

* Access Conditions**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../concepts/access-conditions.md) - Enforce detailed rules such as time-based or geo-based restrictions, to tailor access policies to your needs.

* [Reporting](#reporting) - Tools to help you monitor and analyze workload interactions in your sandbox environment, providing insights into policy effectiveness and system health.

With these enhancements, Aembit empowers you to make the most of your sandbox setup and prepare for more advanced scenarios.

## Before you begin

[Section titled “Before you begin”](#before-you-begin)

You must have completed the following *before* starting this guide:

* [Aembit quickstart guide](quickstart-core.md) and it’s prerequisites.

## Configure a Trust Provider

[Section titled “Configure a Trust Provider”](#configure-a-trust-provider)

Trust Providers allow Aembit to verify workload identities without relying on traditional credentials or secrets. By using third-party systems for authentication, Trust Providers make sure that only verified workloads can securely interact with your resources. These steps use Docker Desktop Kubernetes deployments.

Returning to the Access Policy Builder

If you’re continuing directly from the core quickstart, your Access Policy should still be open in the builder.

If you’re returning later, navigate to **Access Policies** and select the policy you created.

1. From your Aembit Tenant, go to **Access Policies** and select the Access Policy you created in the quickstart guide.

2. In the **Trust Provider** card in the right panel, click **+ Configure**.

3. Configure the Trust Provider:

   * **Name** - `QuickStart Kubernetes Trust Provider` (or another user-friendly name)

   * **Trust Provider** - `Kubernetes Service Account`

4. In the **Match Rules** section, click **+ New Rule**, then enter the following values:

   * **Attribute** - `kubernetes.io { namespace }`.

   * **Value** - `aembit-quickstart`.

5. Select **Upload Public Key**.

6. Browse for the `.pub` file or copy its contents and paste them into the **Public Key** field:

   Obtain the public key specific to your environment.

   Use the following locations for your operating system:

   * **Windows** - `%USERPROFILE%\AppData\Local\Docker\pki\sa.pub`

   * **macOS** - `~/Library/Containers/com.docker.docker/pki/sa.pub`

   ![Configuring Trust Provider](https://docs.aembit.io/_astro/quickstart_trust_provider.Z7Xmbe8b_Z1UsLM4.webp)

7. Click **Save** to add the Trust Provider to the policy.

By associating this Trust Provider with an Access Policy, Aembit validates workload identities based on the rules you defined.

For example, Aembit automatically authenticates Kubernetes service accounts running in the `aembit-quickstart` namespace and denies accounts from all other namespaces. This makes sure that only workloads within that namespace can access your sensitive resources.

Aembit supports a wide variety of Trust Providers tailored for different environments:

* [Kubernetes Service Account](../../user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider.md)

* [AWS roles](../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md)

* [Azure Instance Metadata Service](../../user-guide/access-policies/trust-providers/azure-metadata-service-trust-provider.md)

This flexibility allows you to seamlessly integrate Trust Providers that align with your existing infrastructure. For more details on Trust Providers, including advanced configurations and other types, see [Trust Provider Overview](../../user-guide/access-policies/trust-providers/add-trust-provider.md) and related sub-pages.

## Configure Access Conditions

[Section titled “Configure Access Conditions”](#configure-access-conditions)

Access Conditions allow you to define specific rules to control when and how Aembit issues credentials to Server Workloads. Access Conditions strengthen security by making sure Aembit grants access only when the Access Conditions aligns with your organization’s policies.

Paid feature

Access Conditions are a paid feature. To enable this feature, contact [Aembit Support](https://aembit.io/support/).

1. In the **Access Conditions** card in the right panel, click **+ Configure**.

2. Configure the Access Condition:

   * **Name** - `QuickStart Time Condition` (or another user-friendly name)

   * **Integration** - `Aembit Time Condition`

3. In the **Conditions** section, select the appropriate timezone for your condition.

4. Click the **+** icon next to each day you want to include in your Time Condition configuration, such as Monday from 8 AM to 5 PM.

   Include your current time

   Make sure your current time falls within the period you set so the condition remains in effect while following this guide.

5. Click **Save** to add the Access Condition to the policy.

   ![Configuring Access Condition](https://docs.aembit.io/_astro/quickstart_access_condition.cr-LKOWb_Zazhua.webp)

6. Click **Save Policy** in the header bar to save all changes.

With this configuration, Aembit grants access to the workloads you specified only during the days and timeframes you defined. If the conditional access check fails, Aembit denies access, and an displays an error message on the client workload. Aembit logs this action and detailed information about the failure, including the `accessConditions` field with an `Unauthorized` result, which you can find in the associated logs.

In the next section, [Reporting](#reporting), you’ll see how to review these logs.

Aembit also supports other types of Conditional Access configurations, such as [GeoIP restrictions](../../user-guide/access-policies/access-conditions/aembit-geoip.md) and integrations with third-party vendors such as [CrowdStrike](../../user-guide/access-policies/access-conditions/crowdstrike.md). These options allow you to build comprehensive and flexible access policies suited to your organization’s needs. For more details on Access Conditions, see [Access Conditions Overview](../../user-guide/access-policies/access-conditions/overview.md) and explore related sub-pages to configure additional types.

## Reporting

[Section titled “Reporting”](#reporting)

Reporting is crucial for maintaining security and operational efficiency. It provides a clear view of access attempts, policy evaluations, and credential usage, enabling you to identify potential issues and maintain compliance.

To access the Reporting Dashboard, in your Aembit Tenant, select **Reporting** from the left sidebar menu. By default, you’ll see the **Access Authorization Events** page, where you can review event details related to workload access attempts.

In the top ribbon menu, there are three key reporting categories:

* **Access Authorization Events** - View event logs for all access attempts. Each event details its evaluation stages, showing which Access Policies Aembit applied, whether they succeeded, and the reason for any failures.

* **Audit Logs** - Track system changes, such as user actions, configuration updates, or policy changes.

* **Workload Events** - Monitor events generated from the traffic between Client Workloads and Server Workloads. These events provide detailed information about all requests and responses, helping you analyze workload interactions comprehensively.

![Reporting Dashboard](https://docs.aembit.io/_astro/quickstart_reporting_dashboard.wQyXnMMW_eU8aE.webp)

You also have filters available to you to narrow down your view by **Timespan**, **Severity**, and **Event Type**. These filters help you analyze events more efficiently, focusing on specific time periods or issues that require your attention.

For now, you’ll look at **Access Authorization Events**. As they provide essential insight into how Aembit evaluates access requests.

### Access Authorization Events

[Section titled “Access Authorization Events”](#access-authorization-events)

Whenever a Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../concepts/client-workloads.md) attempts to access a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../concepts/server-workloads.md), Aembit generates Access Authorization Events. These events capture access attempts, log how Aembit evaluated access, and display the outcome (granted or denied). The process has three stages:

* **Access Request** - Captures initial request details, including source, target, and transport protocol.

* **Access Authorization** - Evaluates the request against Access Policies, detailing results from Trust Providers, Access Conditions, and Credential Providers**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../concepts/credential-providers.md).

* **Access Credential** - Shows how Aembit retrieved and injected credentials, or explains any failure reasons.

To review these stages, follow these steps:

1. **Filter by Request** - In the filtering options, locate the **Event Type** and select **Request**. Then, click an event in the list to inspect it.

   ![Access Request Event](https://docs.aembit.io/_astro/quickstart_reporting_access_request.DrDC9hSq_1lNMiB.webp)

   This event provides key details about the connection attempt. It shows when the request happened, where it’s coming from, and which workload made the request. For the quickstart, you should see:

   * **Target Host** - `aembit-quickstart-server.aembit-quickstart.svc.cluster.local`

   * **Service Account** - `aembit-quickstart-client`

   Both should match what you configured in the Access Policy.

2. Filter by **Authorization** - Change the **Event Type** filter to **Authorization** and select an event from the list.

   ![Access Authorization Event](https://docs.aembit.io/_astro/quickstart_reporting_access_auth.Blrjygp8_28gs2F.webp)

   This event shows how Aembit evaluated access against the Access Policy. It displays the result (**Authorized** or **Unauthorized**) and highlights key components that Aembit checked. For the quickstart sandbox environment, you’ll see that Aembit successfully:

   * Identified the Client Workload, Server Workload, and Access Policy.

   * Attested the Trust Provider.

   * Verified the Access Condition.

   * Identified the Credential Provider.

   When Aembit successfully identifies and verifies these components, Aembit grants access to that Client Workload.

3. **Filter by Credential** - Change the **Event Type** filter to **Credential** and select an event from the list.

   ![Access Credential Event](https://docs.aembit.io/_astro/quickstart_reporting_access_credential.DkRU8My8_ZwoYGM.webp)

   This event tracks how Aembit retrieves credentials to enable access. It shows whether Aembit was successful in retrieving the credential and which Credential Provider Aembit used.

   For the quickstart sandbox environment, you’ll see that Aembit successfully:

   * Identified the Client Workload, Server Workload, and Access Policy.

   * Retrieved the Credential Provider, verifying that the Client Workload had the required credentials for secure access.

At this stage, everything is in place; the request was successfully authorized, credentials were securely retrieved, and the Client Workload can now access the Server Workload.

For more detailed insights into Access Credential Events and other reports, visit the [Reporting](../../user-guide/audit-report/overview.md) page. These pages provide further guidance on using filters, understanding event data, and troubleshooting potential issues.

Quickstart completed!

Congratulations on completing the quickstart! You now have a solid foundation in Aembit’s key capabilities. This is just the beginning, and Aembit has much more to offer! Aembit’s full documentation provides in-depth guides and advanced techniques to help you expand your access policies and strengthen workload identity management.

For your next steps, you can either try configuring Aembit with your real client workloads or explore additional possibilities to tailor it to your needs. In both cases, see the following resources:

* **Server Workload Cookbook** - Offers ready-to-use recipes for popular APIs and services. Explore guides such as [Salesforce REST](../../user-guide/access-policies/server-workloads/guides/salesforce-rest.md) and [GitHub REST](../../user-guide/access-policies/server-workloads/guides/github-rest.md) to learn how to authorize secure access to these resources.

* **Exploring Deployment Models** - Aembit supports diverse deployment environments beyond Kubernetes. For detailed examples and guidance, visit the [Support Matrix](../../reference/support-matrix.md) and explore related sub-pages to learn about configuring deployments for specific environments like [Virtual Machines](../../user-guide/deploy-install/virtual-machine/overview.md), [AWS Lambda Containers](../../user-guide/deploy-install/serverless/aws-lambda-container.md), and more.

Check out these guides and more to optimize your workloads with confidence!

## Next steps

[Section titled “Next steps”](#next-steps)

* [Core concepts](../concepts/overview.md) - Understand Aembit’s core concepts and how they work together.
* [Aembit User Guide](../../user-guide/overview.md) - Dive deeper into Aembit’s features and capabilities.
* [Aembit API Guide](../../api-guide/overview.md) - Access detailed technical documentation.
