---
type: how-to
title: "Manage MCP user sessions"
description: "View, filter, and revoke MCP user sessions on an Identity Provider, and revoke them when you delete one."
resource: https://docs.aembit.io/user-guide/administration/identity-providers/manage-mcp-user-sessions/
interface: web-ui
tags: ["identity-provider", "administration"]
timestamp: 2026-10-13T14:46:03-07:00
---

# Manage MCP user sessions

Each Identity Provider’s **Sessions** tab lists the MCP user sessions of the people who signed in through it. Revoke a session to cut off the access that one sign-in through an MCP Client granted, or revoke every session for a user or an MCP client at once. For what a session is and what revoking one does, see [MCP user sessions](../../access-policies/mcp-identity-gateway/mcp-user-sessions.md).

## Prerequisites

* A role with **Read Only** or **Read/Write** permission on Identity Providers to view sessions, and **Read/Write** to revoke them. See [Roles overview](../roles/overview.md).
* An Identity Provider that users sign in through to reach the MCP Identity Gateway.
* A Client-to-Gateway Access Policy whose Credential Provider is an [OIDC ID Token](../../access-policies/credential-providers/oidc-id-token.md) Credential Provider with **Enable Refresh Token Support** turned on. Other Credential Providers don’t create sessions.

## View sessions

1. Log in to your Aembit Tenant.

2. Click **Administration** in the left sidebar, then select **Identity Providers**.

3. Click the Identity Provider that your users sign in through.

4. Click the **Sessions** tab.

   The **User Sessions** table lists each session with these columns:

   | Column         | What it shows                                                             |
   | -------------- | ------------------------------------------------------------------------- |
   | **User Email** | The user who signed in                                                    |
   | **MCP Client** | The MCP client the user signed in through                                 |
   | **Issuance**   | When the user signed in                                                   |
   | **Expiration** | When the session’s refresh tokens stop working                            |
   | **Status**     | **Active**, or **Revoked** for a session that you revoked or that expired |
   | **Actions**    | The **Revoke session** icon                                               |

5. (Optional) To narrow the list, type part of an email address in **Filter Users**, select an MCP client from **Filter MCP Client**, or both.

The **Sessions** tab is available only after you save the Identity Provider. Revoked and expired sessions stay in the list for a time after they end.

## Revoke a session

1. On the **Sessions** tab, find the session you want to revoke.

2. In the **Actions** column, click the **Revoke session** icon.

3. In the **Revoke User Session** dialog, click **Revoke**.

   Aembit Tenant UI shows “Session revoked successfully.”

The revoke takes effect within about a minute. The user can sign in again from the same MCP client, which creates a new session.

## Revoke a user’s or an MCP client’s sessions

**Revoke filtered** revokes every active session that matches the current filters.

1. On the **Sessions** tab, set the filters to the sessions you want to revoke:

   * To revoke a user’s sessions, type their email address in **Filter Users**.
   * To revoke an MCP client’s sessions, select it from **Filter MCP Client**.
   * To revoke one user’s sessions in one MCP client, set both.

2. Check every page of the list, and make sure it shows only the sessions you want to revoke. **Filter Users** matches every email address that contains what you type, so `bob@example.com` also matches `jimbob@example.com`.

   If the list shows sessions you want to keep, [revoke the others one at a time](#revoke-a-session) instead.

3. Click **Revoke filtered**.

4. In the confirmation dialog, click **Revoke All** when you filtered by user only, or **Revoke Filtered** when you selected an MCP client.

The **Sessions** tab has no action that revokes every session on an Identity Provider at once. To do that, use the `POST /api/v1/sso-idps/{id}/user-sessions/revoke` endpoint with `revokeAll` set to `true`. See the [Aembit Cloud API](../../../dev-guide/api/cloud/overview.md).

## Revoke sessions when deleting an Identity Provider

When you delete an Identity Provider that still has active sessions, the delete dialog offers to revoke them first.

1. On the Identity Provider’s **Details** tab, turn off the toggle to deactivate the Identity Provider. If a confirmation dialog appears, click **Yes**. You can delete only an inactive Identity Provider.

2. Click the three-dot menu, then select **Delete**.

3. In the dialog, keep **Revoke all active user sessions before deleting** checked, and click **Revoke Sessions & Delete**.

If you clear **Revoke all active user sessions before deleting**, the button changes to **Delete**. The sessions then keep working until they expire, and you can’t revoke them later, because their Identity Provider no longer exists. To cut off those users afterward, deactivate the Client-to-Gateway Access Policy that admits them, or its Credential Provider. Deactivating the Access Policy cuts off every user it admits. Deactivating the Credential Provider cuts off every Access Policy that uses it. Either way, access ends once the current access tokens expire and the next refresh fails.

## Related

* [MCP user sessions](../../access-policies/mcp-identity-gateway/mcp-user-sessions.md)
* [OIDC ID Token Credential Provider](../../access-policies/credential-providers/oidc-id-token.md)
* [Access Authorization Events](../../audit-report/access-authorization-events.md)
* [Identity Providers overview](overview.md)
