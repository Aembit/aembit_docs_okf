---
type: how-to
title: "User Group"
description: "How to identify MCP Identity Gateway users by the groups they belong to in an OIDC Identity Provider"
resource: https://docs.aembit.io/user-guide/access-policies/client-workloads/identification/user-group/
interface: web-ui
tags: ["identification", "client-workload", "access-policy"]
timestamp: 2026-11-03T11:36:11-05:00
---

# User Group

The **User Group** identifier matches a Client Workload to users by the groups they belong to in an OIDC Identity Provider (IdP). Use it to limit an MCP server that the MCP Identity Gateway exposes to the members of a team, such as `engineering` or every group that starts with `finance-`.

Aembit reads each user’s groups when they sign in through the Identity Provider. To set which claim Aembit reads and how long Aembit caches groups, see [Configure user group lookup](../../../administration/identity-providers/user-group-lookup.md).

## Applicable deployment type

This method is for [MCP Identity Gateway](../../../deploy-install/mcp-identity-gateway/overview.md) deployments. Add it to the Client Workload of a Gateway-to-server Access Policy, beside the identifier that identifies your MCP Identity Gateway.

On a request, Aembit evaluates a User Group identifier only when the request carries an identity token from the Aembit MCP Authorization Server. Aembit issues that token after the user signs in through an Identity Provider. A request with any other token, such as an OIDC ID token from a CI/CD pipeline, never matches a User Group identifier.

When the Gateway-to-server Access Policy uses the [MCP User-Based Access Token](../../credential-providers/about-mcp-user-based-access-token.md) type of Credential Provider, Aembit also checks groups while the user signs in. Aembit leaves that Access Policy’s MCP server out of the sign-in when the user’s groups do not match its Client Workload. This check needs the Client-to-Gateway Access Policy to use the [OIDC ID Token](../../credential-providers/oidc-id-token.md) Credential Provider.

## Prerequisites

* An active OIDC 1.0 Identity Provider that your users sign in through. Aembit collects groups only from OIDC 1.0 Identity Providers.
* Group lookup configured on that Identity Provider, so Aembit can read groups from UserInfo and keep them current. See [Configure user group lookup](../../../administration/identity-providers/user-group-lookup.md).
* At least one sign-in by a member of each group you want to match. Aembit does not read your directory, so it knows only the groups of users who have signed in.

## How group matching works

Each User Group value has two parts: an **Identity Provider** and a **Group Name or Pattern**. The value matches a user when the user signed in through that Identity Provider and one of the user’s groups matches the name or pattern. A group with the same name from a different Identity Provider does not match.

Patterns follow these rules:

* `*` matches any run of characters, at the start, middle, or end of the pattern. Every other character, including `?`, matches itself.
* The pattern must match the whole group name, so `qa` does not match `qa-engineers`.
* Matching is case-sensitive, so `QA-ENGINEERS` does not match `qa-engineers`.

A user in the `qa-engineers` group matches these patterns as follows:

| Group Name or Pattern | Matches `qa-engineers` |
| --------------------- | ---------------------- |
| `qa-engineers`        | Yes                    |
| `qa-*`                | Yes                    |
| `*-engineers`         | Yes                    |
| `qa`                  | No                     |
| `QA-*`                | No                     |

When a Client Workload has more than one User Group value, the user needs to match any one of them. This holds for values that name different Identity Providers too, so one Client Workload can serve users from two Identity Providers.

Identifiers of every other type on the same Client Workload must also match. For example, a Client Workload with an **OIDC ID Token Audience** identifier and a User Group identifier matches only requests that pass both.

### Limitations

* A Client Workload with a User Group identifier cannot also use the [Redirect URI](redirect-uri.md) or [CIMD Client ID](cimd-client-id.md) identifier. Those identifiers identify the MCP client, and the Aembit Tenant UI hides them after a Client Workload has any other identifier type.
* The **Identity Provider** list includes SAML 2.0 Identity Providers. A User Group value scoped to a SAML 2.0 Identity Provider never matches, because Aembit collects groups only from OIDC 1.0 Identity Providers.
* A group removed in your IdP can keep matching for up to the Identity Provider’s group cache time, and longer while group refreshes fail. See [When changes take effect](../../../administration/identity-providers/user-group-lookup.md#when-changes-take-effect).

## Create a Client Workload that matches user groups

To configure a Client Workload using the User Group identifier, follow these steps:

1. Log in to your Aembit Tenant.

2. Click **Client Workloads**.

3. Click **+ New** to open the Client Workload editor panel.

   To add a User Group identifier to an existing Client Workload, open it, click **Edit**, and click **Additional Client Identifier**.

4. Enter the **Name** and optional **Description** for the Client Workload.

5. Under **Client Identification**, select **User Group**.

6. For **Identity Provider**, select the OIDC 1.0 Identity Provider your users sign in through.

7. For **Group Name or Pattern**, enter a group name, or a pattern that uses `*`.

   As you type, Aembit suggests groups that start with what you typed, from users who signed in through the selected Identity Provider.

   For example: `engineering-*`

8. To match more groups, click **Additional Client Identifier**, select **User Group**, and repeat the previous two steps.

   A user needs to match any one of the User Group values.

9. Click **Save**.

   Aembit displays the new Client Workload on the **Client Workloads** page.

## Troubleshoot a group mismatch

When a user cannot reach an MCP server that a User Group identifier gates, open the request’s event in [Access Authorization Events](../../../audit-report/access-authorization-events.md). The Client Workload entry shows the result `NotFound`. Some events also show the reason `MatchRuleFailed` and an attribute that starts with `userGroup:`, followed by the Identity Provider’s ID, when the Client Workload’s other identifiers matched.

Check the following:

* **Identity Provider** - The user signed in through the Identity Provider the User Group value names.
* **Group name** - The group appears as a suggestion in **Group Name or Pattern** for that Identity Provider. If it does not, no member has signed in through that Identity Provider yet, or the IdP sends no claim by that name.
* **Case** - The name or pattern uses the same case as the group name in your IdP.
* **Stale groups** - The user joined the group after their last sign-in. Ask the user to sign in again, so Aembit reads their current groups when the IdP sends the group claim.
* **Identity Provider type** - The Identity Provider is OIDC 1.0, not SAML 2.0.
