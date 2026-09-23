---
type: how-to
title: "Prepare the Identity Provider for enterprise-managed access"
description: "Configure your OIDC Identity Provider and Okta so Aembit can exchange a user's SSO identity for an MCP server access token"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/mcp-enterprise-managed-access-token-idp/
interface: web-ui
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-22T15:44:57-07:00
---

# Prepare the Identity Provider for enterprise-managed access

An MCP Enterprise Managed Access Token Credential Provider depends on two changes at the corporate identity provider. The identity provider must return a refresh token to Aembit, and it must allow the token exchange for the MCP server.

See [About the MCP Enterprise Managed Access Token Credential Provider](about-mcp-enterprise-managed-access-token.md) for how the token exchange works.

## Before you start

* **An OIDC 1.0 Identity Provider that your users sign in through.** Create it in your Aembit Tenant and link it to an Okta OIDC application. See [How to create an OIDC 1.0 Identity Provider](../../administration/identity-providers/create-idp-oidc.md).
* **Administrator access to the Okta org.** The org must have Cross App Access (Okta’s name for Enterprise-Managed Authorization) available on it.
* **An MCP server that supports Enterprise-Managed Authorization.** The MCP server must also trust your Okta org as its identity provider. The MCP server vendor’s documentation covers that step.
* **A Teams or Enterprise subscription plan.** The Identity Providers feature isn’t available on the Starter plan.

## Add the `offline_access` scope in Aembit

1. Log into your Aembit Tenant.

2. Click **Administration** in the left sidebar.

3. At the top, select **Administration ☰ Identity Providers**.

4. Open the OIDC Identity Provider that your users sign in through.

5. Add `offline_access` to the **Identity Provider Scopes** field.

6. Click **Save**.

Aembit stores a refresh token for a user only when the Identity Provider returns one, and an Identity Provider returns one only for this scope.

## Enable token exchange on the Okta application

1. In the Okta Admin Console, open the OIDC application that Aembit signs users in through.

2. On the **General** tab, under grant types, enable **Refresh Token** and **Token Exchange**, and turn off **Client Credentials**.

3. On the **Resource Server** tab, enable **Cross-app access**.

4. Set the issuer to the MCP server’s authorization server issuer, which is the same value that you enter as **OIDC Issuer URL** when you create the Credential Provider. For Atlassian, the issuer is `https://auth.atlassian.com/VCeDsk8ZHncYF1g234fKtc4lNipbBhu3`.

   Set the audience, which Okta labels **Audience/tenant ID**, to the value that the MCP server vendor specifies. For Atlassian, that value is your Atlassian site’s cloud ID. To find it, open `https://<your-site>.atlassian.net/_edge/tenant_info` and copy the `cloudId` value.

5. Save your changes.

## Create the Credential Provider

Create the MCP Enterprise Managed Access Token Credential Provider now, and click **Discover** to fill the scopes and the endpoint URLs. **Discover** also fills the **Client ID** when the MCP server supports dynamic client registration; otherwise, enter the **Client ID** yourself. Copy the **Client ID** from the Credential Provider, because [Register the AI agent in Okta](#register-the-ai-agent-in-okta) needs it. See [Configure MCP Enterprise Managed Access Token](mcp-enterprise-managed-access-token.md).

## Register the AI agent in Okta

1. In the Okta Admin Console, go to **Directory** and then **AI Agents**, and click **Register AI Agent**.

2. Enter a name, click **Next**, and select the OIDC application from [Enable token exchange on the Okta application](#enable-token-exchange-on-the-okta-application).

3. Click **Next**, and then register the agent.

4. Open the registered agent and go to the **Resource Connections** tab.

5. Click **Add resource Connection**, choose the OIDC application instance, and in **AI agent’s client ID registered in this app**, enter the **Client ID** from [Create the Credential Provider](#create-the-credential-provider).

6. Click **Next** and finish the connection.

## Have users sign in again

Aembit captures the refresh token when a user signs in, so a user who signed in before [adding the `offline_access` scope](#add-the-offline_access-scope-in-aembit) and [enabling token exchange](#enable-token-exchange-on-the-okta-application) has no refresh token stored. Those users must sign out of Aembit and sign in again through the Identity Provider.

## Verify the setup

Attach the Credential Provider to a Gateway-to-Server Access Policy, as described in [Create the gateway-to-server Access Policy](../mcp-identity-gateway/setup-mcp-gateway.md#create-the-gateway-to-server-access-policy). Have a user make a request through the MCP Identity Gateway, and then open Reporting and find the [`access.credential` event](../../audit-report/access-authorization-events.md#access-credential-events) for that request. A result of `Retrieved` means that both the identity assertion request and the access token request succeeded. For a result of Error, see [Troubleshoot MCP and AI IAM access](../../troubleshooting/mcp-ai-iam.md).

## Related topics

* [About the MCP Enterprise Managed Access Token Credential Provider](about-mcp-enterprise-managed-access-token.md) - How this Credential Provider works and when to use it
* [Configure MCP Enterprise Managed Access Token](mcp-enterprise-managed-access-token.md) - How to create the Credential Provider that these changes support
* [How to create an OIDC 1.0 Identity Provider](../../administration/identity-providers/create-idp-oidc.md) - How to add the OIDC Identity Provider that your users sign in through
* [MCP Identity Gateway setup](../mcp-identity-gateway/setup-mcp-gateway.md) - How to configure the Gateway-to-Server Access Policy that uses this Credential Provider
* [Troubleshoot MCP and AI IAM access](../../troubleshooting/mcp-ai-iam.md) - How to investigate a failed credential retrieval
