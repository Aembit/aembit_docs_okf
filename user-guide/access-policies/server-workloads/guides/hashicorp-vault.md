---
type: how-to
title: "HashiCorp Vault"
description: "This page describes how to configure Aembit to work with the HashiCorp Vault Server Workload."
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/hashicorp-vault/
interface: web-ui
tags: ["security", "guide", "server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# HashiCorp Vault


[HashiCorp Vault](https://www.vaultproject.io/) is a secrets management platform designed to secure, store, and control access to sensitive data and cryptographic keys.

Below you can find the Aembit configuration required to work with the HashiCorp Vault service as a Server Workload using the a Vault CLI, or HTTP API.

## Prerequisites

Before proceeding with the configuration, ensure you have the following:

* Vault Cluster (self-hosted or HCP tenant).
* An OIDC authentication method enabled in your Vault cluster. If you have not already set this up, follow the steps outlined in the next section or refer to the [official HashiCorp Vault documentation](https://developer.hashicorp.com/vault/tutorials/auth-methods/oidc-auth) for more detailed instructions.

### Configure Vault

1. Log in to your Vault cluster.

2. In the left pane, select **Authentication methods**, and then click on **Enable new method** from the top-right corner.

3. Choose the **OIDC** radio-button and click **Next**.

4. Choose a name for the **Path**. The `oidc/<name>` format is the Hashicorp recommended format. Then click on **Enable Method**.

5. In the Configuration page, configure the OIDC according to your preferences. Below are key choices:

   * For the **OIDC discovery URL** field, navigate to Aembit UI, create a new Credential Provider, choose **Vault Client Token**, and copy the auto-generated Issuer URL. Paste it into Vault’s **OIDC discovery URL** field. Make sure not to include a slash at the end of the URL.
   * If you do not set a **Default Role** for the Vault Authentication method, make sure to include a role name for configuration in the Aembit Credential Provider.

6. After making all your configurations, click **Save**.

### Configure Vault Role

After completing the configuration on Vault, creating a Vault Role for the associated Vault Authentication Method is essential. To do this, navigate to the Vault CLI shell icon (>\_) to open a command shell, and within the terminal, execute the following command:

```shell
$ vault write auth/$AUTH_PATH/role/$ROLE_NAME \
   bound_audiences="$AEMBIT_ISSUER" \
   user_claim="$USER_CLAIM" \
   token_policies="$POLICY_VALUE" \
   role_type="jwt"
```

:warning: Before running the command, ensure you have replaced the variables (e.g. `$AUTH_PATH`, `$ROLE_NAME`, etc.) with your desired values and `$AEMBIT_ISSUER` with the Issuer URL copied from the Aembit Credential Provider.

## Credential Provider Configuration

1. Create a new Credential Provider.

* **Name** - Choose a user-friendly name.
* **Credential Type** - [Vault Client Token](../../credential-providers/vault-client-token.md)

JSON WEB TOKEN (JWT)

* **Subject** - Test (In this example, ‘Test’ is used as a value, but this field can accept any Vault-compatible subject value.)
* **Issuer (Read-Only)** - An auto-generated OpenID Connect (OIDC) Issuer URL from Aembit Edge, used during Vault method configuration.

CUSTOM CLAIMS

* **Claim Name** - vault\_user
* **Value** - empty (In this example, ‘empty’ is used as a value, but this field can accept any string input.)

VAULT AUTHENTICATION

* **Host** - Hostname of your Vault Cluster (e.g. `vault-cluster-public-vault-xyz.abc.hashicorp.cloud`)
* **Port** - 8200 with TLS is recommended. Please use the configuration which matches your Vault cluster.
* **Authentication Path** - Provide the path name of your OIDC Authentication method (e.g. oidc/path).
* **Role** - If you did not set the **Default Role** previously, a role name must be provided here; otherwise optional.
* **Namespace** - Provide the **namespace** used in Vault. You can find it at the bottom left corner of the page. (optional)
* **Forwarding Configuration** - No Forwarding (default)

### Configuration-Specific Fields

Depending on your Vault Role configuration, ensure that the Credential Provider includes the following values:

* **Subject** - If using a `bound_subject` configuration for your Vault Role, this value must match that configuration.

CUSTOM CLAIMS

* **Claim Name** - aud
* **Value** - This value should match the configuration in your Vault role’s `bound_audiences` setting.

## Server Workload configuration

1. Create a new Server Workload.

* **Name** - Choose a user-friendly name.

2. Configure the Service endpoint:

* **Host** - Hostname of your Vault Cluster (e.g. `vault-cluster-public-vault-xyz.abc.hashicorp.cloud`)
* **Application Protocol** - HTTP
* **Port** - 8200 with TLS is recommended. Please use the configuration which matches your Vault cluster.
* **Forward to Port** - 8200 with TLS is recommended. Please use the configuration which matches your Vault cluster.
* **Authentication method** - HTTP Authentication
* **Authentication scheme** - Header
* **Header** - X-Vault-Token

## Client Workload Configuration

Aembit now handles the credentials required to access the Server Workload, eliminating the need for you to manage them directly. You can safely remove any previously used credentials from the Client Workload.

If you access the Server Workload through an SDK or library, it is possible that the SDK/library may still require credentials to be present for initialization purposes. In this scenario, you can provide placeholder credentials. Aembit will overwrite these placeholder credentials with the appropriate ones during the access process.

## Access Policy

* Create an access policy for a Client Workload to access the HashiCorp Vault Server Workload and assign the newly created Credential Provider to it.

## Required Features

* You will need to configure the [TLS Decrypt](../../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) feature to work with the HashiCorp Vault Server Workload.

## Related

**Compatible credential providers**

* [HashiCorp Vault Client Token](../../credential-providers/vault-client-token.md)
* [OAuth 2.0 Client Credentials](../../credential-providers/oauth-client-credentials.md)
