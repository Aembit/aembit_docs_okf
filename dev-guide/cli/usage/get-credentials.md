---
type: how-to
title: "Getting credentials with Aembit CLI on Linux"
description: "A guide to getting credentials on Linux with Aembit CLI"
resource: https://docs.aembit.io/dev-guide/cli/usage/get-credentials/
interface: cli
tags: ["usage", "cli"]
timestamp: 2026-09-09T09:33:20-07:00
---

# Getting credentials with Aembit CLI on Linux

Follow the steps on this page to use the Aembit CLI to retrieve credentials to access a Server Workload.

The command `aembit credentials get` allows you to obtain credentials that you can use in scripts or applications to access Server Workloads protected by Aembit Access Policies. The command requires you to provide the Edge SDK Client ID, Server Workload host, and Server Workload port as parameters.

In this procedure, you access your Aembit Tenant and run the Aembit CLI in your terminal. You then obtain credentials from a Credential Provider to access a specific Server Workload.

## Prerequisites

Before you can retrieve credentials, ensure you have the following:

* [Aembit CLI installed](setup.md)
* Access to your Aembit Tenant with your fully configured Access Policy
* The Edge SDK Client ID from your [Supported Trust Provider](../overview.md#supported-trust-providers)
* A [supported Credential Provider](../overview.md#supported-credential-providers)
* The hostname and port of the Server Workload you want to access

### About Credential Providers

Your [Credential Provider](../overview.md#supported-credential-providers) determines the type of credentials you can retrieve and how you can use them to access a Server Workload. If you change the Server Workload in an Access Policy, you’ll likely need to change the Credential Provider to match the authentication requirements of the new Server Workload. You can add or remove Client Workloads from Access Policies without modifying the Credential Provider or underlying credentials. The Client Workload just matches the environment where you run the CLI.

This procedure includes two different ways to run the `aembit credentials get` command, depending on the type of credentials your Credential Provider retrieves.

Your Credential Provider also determines which names you can pass to `--credential-names`, so check [Credential name support](../credential-names.md) before you rename a credential.

> **Environment variable and command option priority**
>
> You can configure the Aembit CLI using both environment variables and command options. Command options take precedence, overriding any corresponding environment variables.
>
> For example, if you’ve set a value with the `--client-id` option, Aembit CLI uses that over the `AEMBIT_CLIENT_ID` environment variable. This lets you establish a default configuration with environment variables and override specific settings for individual commands as needed.

## Get credentials to access a Server Workload

To retrieve credentials to access a specific Server Workload, follow these steps:

1. Log into your Aembit Tenant.

2. Follow the steps in [Find your Edge SDK Client ID](../../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md) to obtain your Edge SDK Client ID.

3. Identify the hostname and port of the Server Workload you want the credential for.

   You can do this by checking the Server Workload’s configuration or by checking the Access Policy that applies to the Workload in your Aembit Tenant.

4. Open your terminal that has Aembit CLI installed.

5. Run the `aembit credentials get` command with the required parameters for the type of credential you want to retrieve:

   * Single-value credentials

     Use this approach for [Credential Providers](../overview.md#supported-credential-providers) that output a single credential value.

     The `eval` command executes the CLI output as shell commands, setting the credentials as environment variables in your current shell session.

     **Basic command (sets credential in `TOKEN` environment variable):**

     ```shell
     eval $(aembit credentials get \
       --client-id <clientId> \
       --server-workload-host <host> \
       --server-workload-port <port>)
     ```

     **With custom credential name:**

     ```shell
     eval $(aembit credentials get \
       --client-id <clientId> \
       --server-workload-host <host> \
       --server-workload-port <port> \
       --credential-names MY_ACCESS_TOKEN)
     ```

   * Username & Password

     Use this approach for the [Username & Password](../../../user-guide/access-policies/credential-providers/username-password.md) Credential Provider. This Credential Provider outputs two separate credentials that must use the names `USERNAME` and `PASSWORD`.

     ```shell
     eval $(aembit credentials get \
       --client-id <clientId> \
       --server-workload-host <host> \
       --server-workload-port <port> \
       --credential-names USERNAME,PASSWORD)
     ```

   * Vault Private Network Access

     Use this approach with the [Vault Client Token](../../../user-guide/access-policies/credential-providers/vault-client-token.md) Credential Provider configured for private network access.

     In this configuration, the Credential Provider outputs a credential named `PROXY_CREDENTIAL`.

     ```shell
     eval $(aembit credentials get \
       --client-id <clientId> \
       --server-workload-host <host> \
       --server-workload-port <port> \
       --credential-names PROXY_CREDENTIAL)
     ```

6. Verify that Aembit CLI set the credentials correctly:

   * Single-value credentials

     ```shell
     echo $TOKEN
     # or if you used a custom name:
     echo $MY_ACCESS_TOKEN
     ```

   * Username & Password

     ```shell
     echo $USERNAME
     echo $PASSWORD
     ```

   * Vault Private Network Access

     ```shell
     echo $PROXY_CREDENTIAL
     ```

## Example commands

Here are complete examples using real client IDs and Server Workloads:

**Single-value credential example**:

```shell
eval $(aembit credentials get \
  --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
  --server-workload-host api.example.com \
  --server-workload-port 443)
```

This command retrieves a single credential (like an API token) that you can use to access the Server Workload. Aembit stores the credential in the `TOKEN` environment variable.

**Username & Password examples**:

* *Without* HTTP Basic Auth:

  ```shell
  eval $(aembit credentials get \
    --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
    --server-workload-host database.example.com \
    --server-workload-port 5432 \
    --credential-names USERNAME,PASSWORD)
  ```

* *With* HTTP Basic Auth\
  This is for Server Workloads that use “HTTP Authentication / Basic:

  ```shell
  eval $(aembit credentials get \
    --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
    --server-workload-port 443 \
    --server-workload-host basic-auth.example.com \
    --credential-names USERPASS)


  curl -H "Authorization: Basic $USERPASS" https://basic-auth.example.com
  ```

  The username/password Credential Provider outputs a single Base64-encoded value when used with HTTP Basic Auth. You can choose any name for the credential (like `USERPASS` in the preceding example).

**Vault Private Network Access example**:

```shell
   eval $(aembit credentials get \
     --client-id aembit:useast2:a12bc3:identity:github_idtoken:63ab7be6-9785-4a14-be1c-2acf0253070b \
     --server-workload-host database.example.com \
    --server-workload-port 5432 \
     --credential-names PROXY_CREDENTIAL)
```

This command retrieves a credential named `PROXY_CREDENTIAL` that you can use to access the Server Workload through a Vault Private Network.

## Next steps

Once you’ve retrieved the credentials, you can use them directly in your scripts or applications. The credentials are now available as environment variables in your current shell session.

**Example usage in a script:**

```shell
# Use the credential to make an API call
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/data


# Or with username/password credentials
curl -u "$USERNAME:$PASSWORD" https://api.example.com/secure-endpoint
```

**Important notes:**

* The `eval` command executes the CLI output as shell commands, setting the credentials as environment variables
* Credentials are only available in the current shell session
* To use credentials in a different shell session, you must run the command again
* For troubleshooting common issues, see the [CLI troubleshooting guide](../troubleshooting.md)
