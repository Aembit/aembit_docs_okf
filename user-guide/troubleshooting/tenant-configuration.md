---
type: troubleshooting
title: "Tenant Configuration"
description: "This page describes steps for troubleshooting an Aembit Tenant misconfiguration."
resource: https://docs.aembit.io/user-guide/troubleshooting/tenant-configuration/
interface: web-ui
tags: ["troubleshooting"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Tenant Configuration

### Troubleshooter Tool

Several common misconfigurations can occur. Aembit provides a troubleshooter tool that can detect such misconfigurations.

1. Sign into your Aembit Tenant.
2. Click on the **Help** link in the left sidebar.
3. You will be directed to the **Troubleshooter** tool.

![Troubleshooter](https://docs.aembit.io/_astro/troubleshooter.DqdyyXDI_D363p.webp)

4. Choose the appropriate Client Workload and Server Workload.
5. Click the **Analyze** button.

You will be presented with a view showing various checks that were performed:

* Access Policy Checks
* Client Workload Checks
* Trust Provider Checks
* Access Condition Checks
* Server Workload Checks

![Client Workload Checks](https://docs.aembit.io/_astro/troubleshooter_clientworkload_checks.DubQlFG7_L5BiY.webp) ![Access Conditions Checks](https://docs.aembit.io/_astro/troubleshooter_accessconditions_checks.TB7DAIXR_2ukt9w.webp) ![Server Workload Checks](https://docs.aembit.io/_astro/troubleshooter_serverworkload_checks.C0j3aTRa_Z1Ldktn.webp)

The checks could be in several states:

* A green checkbox icon indicates that the check successfully passed.
* A blue information icon presents general information.
* A yellow exclamation icon indicates that additional configuration may be considered; however, the current configuration is supported and operational.
* A red cross icon indicates that such a configuration will prevent the Client Workload from successfully authenticating to the Server Workload. Such a misconfiguration will have an action item on the right indicating how to rectify the issue.

### Credential Provider Verification

Some Credential Providers, like OAuth 2.0 Client Credentials, allow for the verification of credentials.

1. Sign into your Aembit Tenant.
2. Click on the **Credential Providers** link in the left sidebar.
3. Click on a Credential Provider.
4. Click the **Verify** button.

You will be notified whether the verification succeeded or failed.

In the case of verification failure, please check the credential provider’s details for accuracy.
