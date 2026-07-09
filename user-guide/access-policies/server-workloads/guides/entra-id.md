---
type: how-to
title: "Create an Entra ID Server Workload"
description: "How to configure an Entra ID Server Workload in Aembit using Azure Entra Workload Identity Federation or JWT-SVID Token authentication"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/entra-id/
interface: web-ui
tags: [guide, server-workload, access-policy]
timestamp: 2026-03-12T14:33:26-07:00
---

# Create an Entra ID Server Workload

This guide walks you through creating a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) in Aembit to securely obtain OAuth tokens from Microsoft Entra ID (formerly Azure Active Directory) without storing static client secrets.

**Use this Server Workload** to enable your applications to authenticate to Entra ID-protected resources such as Microsoft Graph API, Azure services, or custom APIs secured by Entra ID.

Aembit supports two authentication approaches for Entra ID:

* **[Azure Entra Workload Identity Federation (WIF)](#azure-entra-workload-identity-federation)** - Aembit directly handles the token exchange with Entra ID
* **[OAuth interception](#oauth-interception)** - For existing applications that already make OAuth requests (zero code changes required). Choose between JWT-SVID Token or OIDC ID Token credential providers.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

**Account access** -

* Access to your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../../get-started/concepts/administration.md) (role: Workload Administrator or higher)
* Access to Azure Portal with permissions to create Entra ID app registrations and federated credentials

**Infrastructure** -

* Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../../../../get-started/concepts/aembit-edge.md) Components deployed in your environment:

  * Agent Proxy installed
  * For VMs: [Linux](../../../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md) or [Windows](../../../deploy-install/virtual-machine/windows/agent-proxy-install-windows.md) installation
  * For Kubernetes: [Kubernetes deployment](../../../deploy-install/kubernetes/overview.md)

* [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) configured on your Agent Proxy. Both authentication approaches require TLS Decrypt because the Agent Proxy must inspect HTTPS traffic to inject credentials. TLS decryption occurs only on the Agent Proxy running alongside your workload.

* Network connectivity from your server to Entra ID endpoints (outbound HTTPS to `login.microsoftonline.com`)

## Choose your authentication approach

[Section titled “Choose your authentication approach”](#choose-your-authentication-approach)

Aembit provides two approaches for authenticating to Entra ID. The OAuth interception approach supports two credential provider types (JWT-SVID Token and OIDC ID Token).

| Aspect                       | Azure Entra WIF CP                                                                                            | JWT-SVID Token                                                                      | OIDC ID Token                                                                    |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| **Best for**                 | New integrations, direct Aembit management                                                                    | Existing OAuth flows, zero code changes                                             | OIDC-based authentication                                                        |
| **Complexity**               | Higher-level abstraction                                                                                      | Lower-level, more flexible                                                          | Lower-level, OIDC standard                                                       |
| **Scope configuration**      | In Credential Provider                                                                                        | In application request                                                              | In application request                                                           |
| **Code changes required**    | May require SDK/config changes                                                                                | None (intercepts existing requests)                                                 | None (intercepts existing requests)                                              |
| **Credential Provider type** | [Azure Entra WIF](../../credential-providers/azure-entra-workload-identity-federation.md) | [JWT-SVID Token](../../credential-providers/spiffe-jwt-svid.md) | [OIDC ID Token](../../credential-providers/oidc-id-token.md) |

**Choose Azure Entra WIF** when:

* You’re building a new integration from scratch
* You want Aembit to manage the complete token exchange
* You can configure your application to use Aembit’s credential flow

**Choose OAuth interception** (JWT-SVID Token or OIDC ID Token) when:

* Your application already makes OAuth token requests to Entra ID
* You need zero-code-change deployment
* You want Aembit to intercept and secure existing OAuth flows

Use **JWT-SVID Token** if you want SPIFFE-compliant tokens, or **OIDC ID Token** if you prefer standard OpenID Connect tokens or want consistency across multiple cloud providers.

## Azure Entra workload identity federation

[Section titled “Azure Entra workload identity federation”](#azure-entra-workload-identity-federation)

This approach uses the Azure Entra Workload Identity Federation (WIF) Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) to directly obtain tokens from Entra ID. Aembit handles the complete token exchange, including federated credential validation.

![Azure Entra Workload Identity Federation flow showing credential request from application through Aembit Cloud to Entra ID](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/guides/entra-id-0.svg)

### Step 1: Configure the Credential Provider

[Section titled “Step 1: Configure the Credential Provider”](#step-1-configure-the-credential-provider)

Follow the complete setup guide for the Azure Entra WIF Credential Provider:

**[Configure an Azure Entra WIF Credential Provider](../../credential-providers/azure-entra-workload-identity-federation.md)**

This guide covers:

* Creating the Credential Provider in Aembit
* Adding a federated credential in your Entra ID app registration
* Configuring the OIDC issuer, audience, and subject mapping
* Verifying the connection

### Step 2: Create the Server Workload

[Section titled “Step 2: Create the Server Workload”](#step-2-create-the-server-workload)

1. Log in to your Aembit Tenant.

2. Go to **Server Workloads**, and click **+ New**.

3. Configure the following fields:

   * **Name**: Enter a descriptive name (for example, `entra-id-graph-api`)
   * **Host**: Enter the target API hostname (for example, `graph.microsoft.com` for Microsoft Graph)
   * **Application Protocol**: Select **HTTP**
   * **Port**: `443`
   * **Forward to Port**: `443` with TLS enabled
   * **Authentication method**: Select **HTTP Authentication**
   * **Authentication scheme**: Select **Bearer**

4. Click **Save**.

### Step 3: Create an Access Policy

[Section titled “Step 3: Create an Access Policy”](#step-3-create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md), the Azure Entra WIF Credential Provider, and the Server Workload. See [Access Policies](../../overview.md) for details.

## OAuth interception

[Section titled “OAuth interception”](#oauth-interception)

This approach intercepts existing OAuth token requests from your application and replaces static credentials with dynamically generated tokens. Your application continues making standard OAuth requests without code changes.

Choose your credential provider type in Step 2:

* **JWT-SVID Token** - Uses JWT-SVID**JWT-SVID**: A SPIFFE Verifiable Identity Document in JWT format. JWT-SVIDs are cryptographically signed, short-lived tokens that prove workload identity and enable secure authentication without static credentials.[Learn more](../../credential-providers/about-spiffe-jwt-svid.md) tokens based on the SPIFFE**SPIFFE**: Secure Production Identity Framework For Everyone (SPIFFE) is an open standard for workload identity that provides cryptographically verifiable identities to services without relying on shared secrets.[Learn more(opens in new tab)](https://spiffe.io/docs/latest/spiffe-about/overview/) standard
* **OIDC ID Token** - Uses standard OpenID Connect tokens

![OAuth interception flow showing Aembit Agent Proxy intercepting token requests between application and Entra ID](https://docs.aembit.io/d2/docs/user-guide/access-policies/server-workloads/guides/entra-id-1.svg)

Before you begin: Plan your Subject value

The **Subject** value must match exactly between Aembit and Azure. Decide on your Subject format before starting (for example, `spiffe://your-domain/workload/entra-client`). You’ll use this same value in both Step 1 (Azure) and Step 2 (Aembit).

### Step 1: Register your application in Entra ID

[Section titled “Step 1: Register your application in Entra ID”](#step-1-register-your-application-in-entra-id)

1. Log in to the Azure Portal and go to **Microsoft Entra ID** -> **App registrations**.

2. Click **New registration** or select an existing application.

3. Note the following values from the **Overview** tab (you’ll need these for Step 3):

   * **Application (client) ID**
   * **Directory (tenant) ID**

4. Assign API permissions required by your workload in **API permissions**.

5. Go to **Certificates & secrets** -> **Federated credentials** tab.

6. Click **Add credential** and configure the federated identity credential:

   | Field                             | Value                                                                                           |
   | --------------------------------- | ----------------------------------------------------------------------------------------------- |
   | **Federated credential scenario** | Other issuer                                                                                    |
   | **Issuer**                        | Leave this tab open - you’ll get this from Aembit in Step 2                                     |
   | **Subject identifier type**       | Explicit subject identifier                                                                     |
   | **Subject**                       | Enter the Subject value you planned (for example, `spiffe://your-domain/workload/entra-client`) |
   | **Audience**                      | `api://AzureADTokenExchange`                                                                    |

   Keep Azure Portal open

   Don’t click **Add** yet. You need the **OIDC Issuer URL** from Aembit (Step 2) to complete the **Issuer** field. Keep this browser tab open and proceed to Step 2.

### Step 2: Create the Credential Provider

[Section titled “Step 2: Create the Credential Provider”](#step-2-create-the-credential-provider)

* JWT-SVID Token

  1. Open a new browser tab and log in to your Aembit Tenant.

  2. Go to **Credential Providers** and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                                                                                   |
     | ------------------- | ------------------------------------------------------------------------------------------------------- |
     | **Name**            | Descriptive name (for example, `entra-id-jwt-svid`)                                                     |
     | **Credential Type** | JWT-SVID Token                                                                                          |
     | **Subject**         | The same Subject value you entered in Azure (for example, `spiffe://your-domain/workload/entra-client`) |
     | **Audience**        | `api://AzureADTokenExchange`                                                                            |
     | **Lifetime**        | 15 minutes (recommended)                                                                                |

     Shorter token lifetimes reduce the window for credential theft if an attacker steals a token. However, shorter lifetimes increase token refresh frequency, adding minor operational overhead. See [Credential Lifecycle](../credential-lifecycle.md) for guidance on choosing lifetimes based on your security requirements.

  4. Click **Save**. After saving, copy the **OIDC Issuer URL** displayed on the Credential Provider details page.

  5. Return to the Azure Portal tab you left open in Step 1.

  6. Paste the OIDC Issuer URL into the **Issuer** field of your federated credential.

  7. Click **Add** to complete the federated credential setup in Azure Portal.

  For detailed configuration options, see [Create a JWT-SVID Token Credential Provider](../../credential-providers/spiffe-jwt-svid.md).

* OIDC ID Token

  1. Open a new browser tab and log in to your Aembit Tenant.

  2. Go to **Credential Providers** and click **+ New**.

  3. Configure the following fields:

     | Field               | Value                                                                                                   |
     | ------------------- | ------------------------------------------------------------------------------------------------------- |
     | **Name**            | Descriptive name (for example, `entra-id-oidc`)                                                         |
     | **Credential Type** | OIDC ID Token                                                                                           |
     | **Subject**         | The same Subject value you entered in Azure (for example, `spiffe://your-domain/workload/entra-client`) |
     | **Audience**        | `api://AzureADTokenExchange`                                                                            |
     | **Lifetime**        | 15 minutes (recommended)                                                                                |

     Shorter token lifetimes reduce the window for credential theft if an attacker steals a token. However, shorter lifetimes increase token refresh frequency, adding minor operational overhead. See [Credential Lifecycle](../credential-lifecycle.md) for guidance on choosing lifetimes based on your security requirements.

  4. Click **Save**. After saving, copy the **OIDC Issuer URL** displayed on the Credential Provider details page.

  5. Return to the Azure Portal tab you left open in Step 1.

  6. Paste the OIDC Issuer URL into the **Issuer** field of your federated credential.

  7. Click **Add** to complete the federated credential setup in Azure Portal.

  For detailed configuration options, see [Create an OIDC ID Token Credential Provider](../../credential-providers/oidc-id-token.md).

### Step 3: Create the Server Workload

[Section titled “Step 3: Create the Server Workload”](#step-3-create-the-server-workload)

Use the **Directory (tenant) ID** you noted from Azure in Step 1.

1. Go to **Server Workloads**, and click **+ New**.

2. Configure the following fields:

   | Field                    | Value                                                                                                                                                           |
   | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **Name**                 | Descriptive name (for example, `entra-id-token-endpoint`)                                                                                                       |
   | **Host**                 | `login.microsoftonline.com`                                                                                                                                     |
   | **Application Protocol** | OAuth                                                                                                                                                           |
   | **Port**                 | `443`                                                                                                                                                           |
   | **Forward to Port**      | `443` with TLS enabled                                                                                                                                          |
   | **URL Path**             | `/{tenant-id}/oauth2/v2.0/token` - Replace `{tenant-id}` with your actual Directory ID (for example, `/12345678-abcd-1234-efgh-123456789abc/oauth2/v2.0/token`) |
   | **Authentication**       | OAuth Client Authentication (POST Body Form URL Encoded)                                                                                                        |

3. Click **Save**.

### Step 4: Create an Access Policy

[Section titled “Step 4: Create an Access Policy”](#step-4-create-an-access-policy)

Create an Access Policy linking your Client Workload, the JWT-SVID Token Credential Provider, and the Server Workload. See [Access Policies](../../overview.md) for details.

### Step 5: Test the integration

[Section titled “Step 5: Test the integration”](#step-5-test-the-integration)

Your application continues making standard OAuth requests. Aembit intercepts the request and replaces the `client_secret` with a `client_assertion` JWT-SVID.

**Test with curl** -

/user-guide/deploy-install/advanced-options/agent-proxy/explicit-steering

```shell
# Set proxy environment variables
export HTTP_PROXY=http://localhost:8080
export HTTPS_PROXY=http://localhost:8080


# Request OAuth token (replace placeholders with your values)
curl -X POST "https://login.microsoftonline.com/{tenant-id}/oauth2/v2.0/token" \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=client_credentials" \
  -d "client_id={client-id}" \
  -d "client_secret=placeholder-value" \
  -d "scope=https://graph.microsoft.com/.default"
```

**Expected response** -

```json
{
  "token_type": "Bearer",
  "expires_in": 3599,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJS..."
}
```

About placeholder credentials

The `client_secret=placeholder-value` is a placeholder that Aembit intercepts and replaces. Use any non-sensitive string. The placeholder never reaches Entra ID. For more patterns, see [Developer Integration](../developer-integration.md).

### Using Microsoft authentication libraries

[Section titled “Using Microsoft authentication libraries”](#using-microsoft-authentication-libraries)

If your application uses Azure.Identity or Microsoft Authentication Library (MSAL) SDK, configure it to use client credentials with a placeholder secret. The Agent Proxy intercepts token requests from these SDKs and injects real credentials.

For SDK-specific code examples and official documentation links, see [Service-specific SDK resources](../developer-integration.md#service-specific-sdk-resources).

## Common configuration

[Section titled “Common configuration”](#common-configuration)

### Azure API scopes

[Section titled “Azure API scopes”](#azure-api-scopes)

The scope determines which API permissions your application can access:

| Azure API              | Scope                                   |
| ---------------------- | --------------------------------------- |
| Microsoft Graph        | `https://graph.microsoft.com/.default`  |
| Azure Resource Manager | `https://management.azure.com/.default` |
| Azure Key Vault        | `https://vault.azure.net/.default`      |
| Azure Storage          | `https://storage.azure.com/.default`    |
| Custom API             | `api://{Application-ID}/.default`       |



Choosing the right scope

The scope you configure determines which API permissions your application can access. Choose based on your security posture and operational requirements:

| Scope Pattern                     | Security Level                            | Use When                             |
| --------------------------------- | ----------------------------------------- | ------------------------------------ |
| **`.default`**                    | Lower (grants all defined permissions)    | Testing, rapid development           |
| **Specific permission**           | Higher (grants only requested permission) | Production with defined requirements |
| **Multiple specific permissions** | Highest (explicit, granular control)      | High-security environments           |

**Environment-based scope strategy** -

* **Development/Test** Use `.default` for faster iteration -
* **Production** Use specific permission scopes following least-privilege principle -

**Real-world examples** -

| Scenario                    | Recommended Scope                                  |
| --------------------------- | -------------------------------------------------- |
| Local development           | `.default`                                         |
| Production API (read-only)  | `api://{AppID}/Data.Read`                          |
| Production API (read/write) | `api://{AppID}/Data.Read api://{AppID}/Data.Write` |
| Financial services          | Separate Server Workloads per permission           |

For more on Entra ID permission scopes, see [Microsoft Entra ID permissions and consent](https://learn.microsoft.com/en-us/entra/identity-platform/permissions-consent-overview).

### Credential lifecycle

[Section titled “Credential lifecycle”](#credential-lifecycle)

Aembit dynamically generates short-lived credentials, eliminating manual rotation. For details on credential rotation, compromise response, and audit logging, see [Credential Lifecycle Management](../credential-lifecycle.md).

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

For common issues like Agent Proxy connectivity, network problems, or TLS configuration, see the [Troubleshooting Guide](../troubleshooting.md).

### Debugging token exchange issues

[Section titled “Debugging token exchange issues”](#debugging-token-exchange-issues)

When token exchange fails, check the Agent Proxy logs to see what credentials Aembit is injecting.

**Linux (systemd):**

```shell
# Monitor Agent Proxy logs for credential events
sudo journalctl --namespace aembit_agent_proxy | grep -i "credential"


# View recent logs with timestamps
sudo journalctl --namespace aembit_agent_proxy --since "5 minutes ago"
```

**Docker/Kubernetes:**

```shell
# Find the Agent Proxy pod
kubectl get pods -n <namespace> | grep agent-proxy


# View Agent Proxy logs (standalone deployment)
kubectl logs <agent-proxy-pod> -n <namespace> -f


# If using sidecar injection
kubectl logs <pod> -n <namespace> -c aembit-agent-proxy -f
```

**What to look for:**

* **Successful token exchange**: Look for log entries referencing credential injection or `GetCredentials` calls
* **Failed token exchange**: Look for error messages about missing policies, invalid credentials, or network failures

To enable more detailed logging, see [Changing Agent log levels](../../../deploy-install/advanced-options/changing-agent-log-levels.md).

This section covers Entra ID-specific issues:

### Application with identifier wasn’t found

[Section titled “Application with identifier wasn’t found”](#application-with-identifier-wasnt-found)

**Symptom** Error message `AADSTS700016: Application with identifier '{client-id}' wasn't found` -

**Cause** The Application (client) ID in your Server Workload or Credential Provider doesn’t match an Entra ID app - registration.

**Solution** -

1. Verify the Application (client) ID in Azure Portal: **Microsoft Entra ID** -> **App registrations** -> **Overview**
2. Update the Client ID in your Aembit Server Workload or Credential Provider configuration
3. Ensure the app registration exists in the correct Azure tenant

### Authorization failed or permission errors

[Section titled “Authorization failed or permission errors”](#authorization-failed-or-permission-errors)

**Symptom** Token request succeeds but your application receives 401 Unauthorized or 403 Forbidden errors. -

**Diagnosis** -

* Check Entra ID sign-in logs: **Microsoft Entra ID** -> **Sign-in logs** -> Filter by Client ID
* Verify API permissions: **App registrations** -> Your app -> **API permissions**

**Solution** -

* Add missing API permissions in Entra ID
* Click **Grant administrator consent** if permissions require it
* Verify the scope in your request matches configured permissions

### Token retrieval fails

[Section titled “Token retrieval fails”](#token-retrieval-fails)

**Symptom** OAuth token request returns an error or times out. -

**Diagnosis** -

```shell
# Test network connectivity to Entra ID
curl -I "https://login.microsoftonline.com"


# Test through Agent Proxy
# Only required for explicit steering: /user-guide/deploy-install/advanced-options/agent-proxy/explicit-steering
export HTTPS_PROXY=http://localhost:8080
curl -I "https://login.microsoftonline.com"
```

**Solution** -

* Verify firewall rules allow outbound HTTPS to `login.microsoftonline.com`
* Confirm you configured [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md)
* Check Agent Proxy logs for errors

### Federated credential validation fails

[Section titled “Federated credential validation fails”](#federated-credential-validation-fails)

**Symptom** Error message `AADSTS70021: No matching federated identity record found` -

**Cause** The OIDC issuer, subject, or audience in the Entra ID federated credential doesn’t match the Aembit - Credential Provider configuration.

**Solution** -

1. In Aembit, note the exact values for:

   * **OIDC Issuer URL**
   * **Subject**
   * **Audience** (should be `api://AzureADTokenExchange`)

2. In Azure Portal, verify the federated credential matches exactly:
   * **Microsoft Entra ID** -> **App registrations** -> Your app -> **Certificates & secrets** -> **Federated credentials**

3. Update any mismatched values

## Cleanup

[Section titled “Cleanup”](#cleanup)

Remove the Entra ID Server Workload

If you no longer need this integration, remove components in this order:

Deactivate Access Policies first

You must deactivate any Access Policies that reference the Server Workload or Credential Provider before you can delete those components. Attempting to delete a Server Workload or Credential Provider that’s in use by an Access Policy results in an error.

1. **Deactivate associated Access Policies** -

   * Go to **Access Policies**
   * Find policies that use this Server Workload or Credential Provider
   * Either deactivate the policy (toggle off) it

2. **Delete the Server Workload in Aembit** -

   * Go to **Server Workloads**
   * Select your Entra ID workload and click **Delete**

3. **Delete the Credential Provider in Aembit** -

   * Go to **Credential Providers**
   * Select the associated Credential Provider and click **Delete**

4. **Delete the Entra ID app registration (optional)** -

   * Azure Portal: **Microsoft Entra ID** -> **App registrations** -> Select application -> **Delete**

Deleting the Server Workload immediately stops credential provisioning. Ensure no applications are actively using this workload before deletion.

## Related resources

[Section titled “Related resources”](#related-resources)

* [Credential Lifecycle Management](../credential-lifecycle.md) - How Aembit manages credential rotation and security
* [Azure Entra WIF Credential Provider](../../credential-providers/azure-entra-workload-identity-federation.md) - Detailed Credential Provider setup
* [JWT-SVID Token Credential Provider](../../credential-providers/spiffe-jwt-svid.md) - JWT-SVID configuration options
* [Developer Integration](../developer-integration.md) - SDK integration and placeholder credentials
* [Architecture Patterns](../architecture-patterns.md) - Understanding OAuth flow and trust boundaries
* [TLS Decrypt Configuration](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) - HTTPS interception setup
