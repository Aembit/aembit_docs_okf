---
type: how-to
title: "Add CrowdStrike AIDR Content Security"
description: "How to add Content Security with CrowdStrike AIDR to an Access Policy in the Access Policy Builder."
resource: https://docs.aembit.io/user-guide/access-policies/content-security/crowdstrike-aidr/add-to-policy/
interface: web-ui
tags: ["crowdstrike-aidr", "content-security", "access-policy"]
timestamp: 2026-09-09T08:20:13-07:00
---

# Add CrowdStrike AIDR Content Security

This guide shows how to add a CrowdStrike AIDR Content Security Provider to an Access Policy in the Access Policy Builder. For how CrowdStrike AIDR inspects traffic and where to get your credentials, see [CrowdStrike AIDR](overview.md).

> **Note**
>
> CrowdStrike AIDR requires an active CrowdStrike AIDR for Agents subscription. To obtain access, contact CrowdStrike.

## Prerequisites

* Access to the Aembit Admin UI with permission to create and edit Access Policies.
* The Access Policy Builder enabled. See [Create an Access Policy](../../create-access-policy.md).
* Your CrowdStrike AIDR base URL and API token. See [Get your credentials](overview.md#get-your-credentials).

## Add CrowdStrike AIDR Content Security to a policy

Configure Content Security from the **Content Security** card in the Access Policy Builder. You enter the connection details for CrowdStrike AIDR directly on the component.

1. In the Aembit Admin UI, open an Access Policy in the Access Policy Builder, or create a new one.

2. In the **Content Security** card, click **+ Configure** to open the Content Security panel.

   ![Content Security card with the Configure action in the Access Policy Builder](https://docs.aembit.io/_astro/apb-content-security-card.CnpOg-Nb_ozQ3a.webp)

3. Leave the **Active** toggle on to enforce Content Security on this policy.

4. Enter a **Name** and optional **Description**.

5. From the **Content Security Type** dropdown, select **CrowdStrike AIDR**.

6. In the **AIDR Base URL** field, enter your CrowdStrike AIDR base URL. The field includes a fixed `https://` prefix.

7. In the **AIDR Token** field, enter your CrowdStrike AIDR API token.

8. (Optional) In the **Timeout** field, set how long, in milliseconds, Aembit waits for an AIDR response before the request times out. The field accepts 5,000 to 600,000 milliseconds and defaults to 5,000.

9. (Optional) In the **Max Retries** field, set how many times Aembit retries a failed AIDR request. The field accepts 0 to 10 and defaults to 2.

10. Set **Fail Open on Error** to control what Aembit does when it can’t reach CrowdStrike AIDR or the request errors:

    * **On** (default) - Aembit forwards the request without inspection (fail open).
    * **Off** - Aembit blocks the request (fail closed).

    For the trade-off between availability and guaranteed inspection, see [Fail-open and fail-closed behavior](overview.md#fail-open-and-fail-closed-behavior).

11. Click **Save** to add Content Security to the policy.

    ![Content Security panel configured with CrowdStrike AIDR connection details](https://docs.aembit.io/_astro/apb-content-security-configured.D5RC772Y_Z1zJYcy.webp)

## Terraform

You can manage Content Security with the [Aembit Terraform Provider](https://registry.terraform.io/providers/Aembit/aembit/latest). See the [`aembit_content_security` resource](https://registry.terraform.io/providers/Aembit/aembit/latest/docs/resources/content_security) for the full configuration reference.
