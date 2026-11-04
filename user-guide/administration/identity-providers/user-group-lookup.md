---
type: how-to
title: "Configure user group lookup"
description: "Turn on group lookup for an OIDC Identity Provider so Access Policies can match your users' groups."
resource: https://docs.aembit.io/user-guide/administration/identity-providers/user-group-lookup/
interface: web-ui
tags: ["identity-provider", "administration"]
timestamp: 2026-11-03T11:36:11-05:00
---

# Configure user group lookup

Aembit can read the groups each user belongs to in your OIDC Identity Provider (IdP). A [User Group](../../access-policies/client-workloads/identification/user-group.md) identifier then matches those groups on a Client Workload, so an Access Policy applies only to members of a team. Group lookup tells Aembit which claim holds the groups, how long to cache them, and whether to ask the IdP’s UserInfo endpoint when the sign-in tokens carry no group claim. The UserInfo endpoint is the standard OIDC endpoint that returns claims about the signed-in user.

## Prerequisites

* An active [OIDC 1.0 Identity Provider](create-idp-oidc.md) in your Aembit Tenant. SAML 2.0 Identity Providers do not support group lookup.
* An IdP that puts group names in a claim, in the ID token, the access token, or the UserInfo response. If your IdP sends the claim only for a requested scope, such as `groups`, add that scope to the Identity Provider’s **Identity Provider Scopes**.
* For background refresh after the access token expires, an IdP that issues refresh tokens. If your IdP issues refresh tokens only for the `offline_access` scope, add that scope too.

## How Aembit gets a user’s groups

Aembit learns a user’s groups when that user signs in through the Identity Provider, whether to the Aembit Tenant UI or through an MCP client. Aembit does not read your directory, so a user who has never signed in has no groups in Aembit.

1. At sign-in, Aembit reads the group claim from the sign-in tokens. Aembit reads the tokens even when group lookup is off.
2. If no token carries the claim and group lookup is on, Aembit requests the claim from the UserInfo endpoint. Aembit uses the UserInfo response only when its `sub` matches the signed-in user.
3. Aembit replaces the user’s stored groups with the groups it found. When UserInfo answers without the claim, Aembit clears the user’s stored groups. When UserInfo fails or Aembit does not call it, the stored groups stay as they were.
4. Aembit caches the groups for the **Group Cache TTL**.
5. When a request needs the user’s groups after the cache time has passed, Aembit uses the last groups it stored and refreshes them from UserInfo in the background. Aembit refreshes at most once a minute for each user and Identity Provider. The refresh needs the user’s access token to still be valid, or a refresh token to get a new one. With group lookup off, Aembit does not refresh, and the groups from the tokens stay until the user signs in again.

Aembit accepts a group claim in any of these forms:

* A JSON array of names, such as `["engineering", "platform"]`
* A comma-separated string, such as `engineering,platform`
* A single name, such as `engineering`

Aembit trims spaces from the start and end of each name and does not split a space-separated string. The claim name matches without regard to case, but group names keep their case.

### When changes take effect

A group a user gains takes effect the next time Aembit reads the user’s groups. Aembit reads them at a sign-in whose tokens or UserInfo response carry the claim, and at a background refresh. A group removed in your IdP can keep matching until the **Group Cache TTL** has passed since Aembit last read the user’s groups. Refreshes fail when the UserInfo endpoint is down or the user has no valid token to refresh with. While they fail, the removed group keeps matching until a sign-in or refresh reads the group claim again. Set a shorter cache time when you need removals to take effect sooner.

When a UserInfo request fails, Aembit keeps the user’s last stored groups. When Aembit holds no groups for a user, no User Group identifier matches that user, and Aembit denies the access that identifier gates.

## Turn on group lookup

1. Log in to your Aembit Tenant.

2. Click **Administration** in the left sidebar, then select **Identity Providers**.

3. Click the OIDC 1.0 Identity Provider your users sign in through, then click **Edit**.

4. On the **Details** tab, turn on the **User Groups & MCP Policy Access** toggle.

   Aembit displays the group lookup settings.

5. In **Group Claim Attribute**, enter the name of the claim that holds group names.

   The default is `groups`. Use the claim name your IdP sends, such as `roles` or a custom claim.

6. In **Group Cache TTL (Minutes)**, enter how long Aembit caches a user’s groups, from 5 to 1440 minutes.

   The default is 15 minutes.

7. Click **Save**.

Ask a user in the group to sign in through the Identity Provider. Their groups then appear as suggestions when you add a [User Group identifier](../../access-policies/client-workloads/identification/user-group.md) to a Client Workload.

## Group lookup settings

| Setting                         | Default    | Allowed values | What it controls                                     |
| ------------------------------- | ---------- | -------------- | ---------------------------------------------------- |
| User Groups & MCP Policy Access | Off        | On or off      | Whether Aembit asks UserInfo and refreshes groups    |
| Group Claim Attribute           | `groups`   | A claim name   | The claim that holds group names                     |
| Group Cache TTL (Minutes)       | 15 minutes | 5 to 1440      | How long Aembit uses groups before it refreshes them |

Each request to the IdP’s discovery document and UserInfo endpoint times out after 5 seconds.

## Check group lookup health

With group lookup on, the Identity Provider’s **Details** tab shows the **UserInfo Health (Last 24 Hours)** card. The card counts the UserInfo requests Aembit made in the current hour and the 23 hours before it, in Coordinated Universal Time (UTC). It counts requests at sign-in and in background refreshes. Click **Refresh** to load the latest counts.

| Field                 | What it shows                                                    |
| --------------------- | ---------------------------------------------------------------- |
| Total Requests        | UserInfo requests in the card’s 24 hourly periods                |
| Successful            | Requests the UserInfo endpoint answered for the signed-in user   |
| Failed                | Requests that failed, timed out, or returned a different `sub`   |
| Success Rate          | Successful requests as a percentage of the total, or N/A at zero |
| Hourly Activity Trend | One bar for each hourly period, split by successful and failed   |

A request counts as successful even when the UserInfo response carries no group claim.

A **No Recent Activity** badge beside the card’s title means Aembit made no UserInfo requests in those 24 hourly periods. When the sign-in tokens already carry the group claim, Aembit calls UserInfo only for background refreshes, so the badge can appear on a working Identity Provider.

A rising **Failed** count means UserInfo lookups are failing, so Aembit is not updating groups from UserInfo, and removed groups keep matching for longer. Check that the IdP’s UserInfo endpoint is reachable and that it returns the same `sub` as the ID token.

## Related

* [User Group identifier](../../access-policies/client-workloads/identification/user-group.md) - Match the groups Aembit collects in a Client Workload
* [Create an OIDC 1.0 Identity Provider](create-idp-oidc.md) - Set up the Identity Provider that group lookup reads from
* [MCP Identity Gateway setup](../../access-policies/mcp-identity-gateway/setup-mcp-gateway.md) - Create the Access Policies that a User Group identifier can restrict
