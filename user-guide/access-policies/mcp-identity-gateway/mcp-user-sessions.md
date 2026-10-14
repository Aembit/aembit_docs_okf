---
type: explanation
title: "MCP user sessions"
description: "What an MCP user session is, what revoking one does and doesn't do, and how a denial appears in events."
resource: https://docs.aembit.io/user-guide/access-policies/mcp-identity-gateway/mcp-user-sessions/
tags: ["mcp-identity-gateway", "access-policy"]
timestamp: 2026-10-13T14:46:03-07:00
---

# MCP user sessions

When a user connects an MCP Client to the MCP Identity Gateway and signs in with your Identity Provider, Aembit can record that sign-in as a user session. Revoking a session cuts off the access that sign-in granted. The Access Policy that admits the user stays unchanged. This page explains what a session covers, what revoking it does and doesn’t do, and how a revoked session appears in events. To view and revoke sessions, see [Manage MCP user sessions](../../administration/identity-providers/manage-mcp-user-sessions.md).

## What a user session is

A user session represents one sign-in by one user through one MCP client. Aembit creates a session only when the sign-in uses an OIDC ID Token Credential Provider that has **Enable Refresh Token Support** turned on. The session lasts as long as the refresh tokens from that sign-in stay valid, which the Credential Provider’s **Absolute Token Lifetime** specifies.

Other Credential Providers don’t create sessions. This includes an OIDC ID Token Credential Provider with refresh token support turned off, an Aembit Access Token Credential Provider, and an MCP Enterprise Managed Access Token Credential Provider.

Each session belongs to the Identity Provider the user signed in with, and that Identity Provider’s **Sessions** tab lists it. Every new sign-in creates a new session, so one user can hold more than one session for the same MCP client. The list shows the user and the MCP client for each session, but not which device the user signed in from.

## What revoking a session does

Revoking a session ends it for every MCP server the user reaches through the MCP Identity Gateway. To control access to one MCP server, change the Gateway-to-Server Access Policy for that server instead.

![Sequence showing an administrator revoking a session and the MCP client's next request being denied](https://docs.aembit.io/d2/docs/user-guide/access-policies/mcp-identity-gateway/mcp-user-sessions-0.svg)

After you revoke a session:

* **Aembit Cloud denies the session’s requests.** Each time Aembit Cloud evaluates an Access Policy for the MCP client’s requests, it checks the session and denies a revoked one. Tool calls fail with an error, and the affected MCP servers drop out of the MCP client’s tool lists.
* **The session’s refresh tokens stop working.** The MCP client can’t exchange a refresh token for a new access token, so the user has to sign in again once the current access token expires.
* **Other sessions keep working.** Revoking one user’s session doesn’t affect other users, or the same user’s sessions in other MCP clients.

A revoke takes effect within about a minute. The MCP Identity Gateway reuses each Access Policy decision for up to a minute, so requests can still succeed until the decision it holds expires. For the same reason, the MCP client can still list tools for up to a minute.

The MCP client keeps its access token until the token expires, and the MCP Identity Gateway still accepts it. Aembit Cloud denies the requests that carry that token, but the MCP client doesn’t ask the user to sign in again until its refresh fails. A notification stream that the MCP client opened before the revoke stays open until that access token expires.

## What revoking a session doesn’t do

* **It doesn’t block the user.** The user can sign in again from the same MCP client, which creates a new session. To keep a user out, remove their access in your Identity Provider so they can’t sign in again, then revoke all their sessions. Deactivating the Client-to-Gateway Access Policy also keeps them out, along with every other user it admits, once their current access tokens expire and the next refresh fails.
* **It doesn’t revoke the user’s authorizations for MCP servers.** OAuth authorizations that the user granted to MCP servers stay in place.
* **It doesn’t change the user’s account at the Identity Provider.** The user’s account stays active at the Identity Provider.
* **It doesn’t apply to Client Workloads that act without a signed-in user.** Only user sign-ins create sessions.

## How a revoked session appears in events

Aembit Cloud records an Access Authorization Event with these values when it denies a request because of a revoked session:

| Field            | Value                        |
| ---------------- | ---------------------------- |
| `meta.severity`  | `Error`                      |
| `outcome.result` | `Unauthorized`               |
| `outcome.reason` | `McpSessionExpiredOrRevoked` |

The same reason appears when a session’s refresh tokens reach the end of their **Absolute Token Lifetime**. The event doesn’t say whether the session expired or you revoked it. The event doesn’t name the session that Aembit denied. For the event’s other fields, see [Access Authorization Events](../../audit-report/access-authorization-events.md).

## Related

* [Manage MCP user sessions](../../administration/identity-providers/manage-mcp-user-sessions.md)
* [OIDC ID Token Credential Provider](../credential-providers/oidc-id-token.md)
* [Set up the MCP Identity Gateway](setup-mcp-gateway.md)
* [Session persistence in the MCP Identity Gateway](../../deploy-install/mcp-identity-gateway/session-persistence-mcp-gateway.md)
