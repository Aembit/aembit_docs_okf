---
type: troubleshooting
title: "Troubleshooting Aembit CLI"
description: "A guide to troubleshooting common issues with Aembit CLI"
resource: https://docs.aembit.io/cli-guide/troubleshooting/
interface: cli
timestamp: 2025-08-05T13:09:07-07:00
type_inferred: true
---

# Troubleshooting Aembit CLI

Aembit CLI is a powerful tool, but you might encounter some common issues while using it. These topics provide solutions to those issues, helping you troubleshoot problems you may encounter.

## Common errors and solutions

[Section titled “Common errors and solutions”](#common-errors-and-solutions)

The following are common errors you might encounter when using the Aembit CLI, along with their solutions:

### No output when using `--credential-names`

[Section titled “No output when using --credential-names”](#no-output-when-using---credential-names)

When you run the command that includes the `--credential-names` flag, such as:

```shell
eval $(aembit credentials get --client-id <clientId> \
  --server-workload-host <host> \
  --server-workload-port <port> \
  --credential-names "USERNAME,PASSWORD")
```

You might notice that there is no output in the terminal, even though Aembit CLI set the credentials as environment variables.

When using the `--credential-names` flag along with the `eval` command, the `aembit credentials get` command sets environment variables directly in your shell, which is a change from previous versions of the CLI.

This means that instead of printing the credentials to the terminal, it sets them as environment variables in your shell.

**Solution** - Use the `echo` command to verify that Aembit set the environment variables correctly:

```shell
echo USERNAME
your-username-value


echo PASSWORD
your-password-value
```

### `TOKEN` Credential mismatch errors

[Section titled “TOKEN Credential mismatch errors”](#token-credential-mismatch-errors)

When running the `aembit credentials get` command without specifying `--credential-names`, you might encounter an error message like this:

```shell
Credential(s) not returned by tenant: TOKEN.
```

This occurs when you’re requesting the default `TOKEN` credential but the matched Access Policy provides username/password credentials instead of a token.

**Solution** - Specify the correct credential names that match what your Access Policy expects for the credential you want to retrieve.

For example, if your Access Policy provides `USERNAME` and `PASSWORD` credentials, you should use the `--credential-names` flag to specify those:

```shell
aembit credentials get --client-id <clientId> \
  --server-workload-host <host> \
  --server-workload-port <port> \
  --credential-names "USERNAME,PASSWORD"
```

Check your Access Policy’s configuration in your Aembit Tenant to ensure it has a matching OIDC token Trust Provider if you’re using or want to use token-based authentication.

### Failed to identify workload errors

[Section titled “Failed to identify workload errors”](#failed-to-identify-workload-errors)

You might encounter error messages indicating that Aembit CLI couldn’t identify either the Client Workload or Server Workload or matched with an Access Policy.

* **Server workload errors** typically show messages like “Failed to identify Server Workload. Matched Client Workload ID: \[client-id]” and occur when the Server Workload isn’t specified correctly (like incorrect port number or host).

* **Client workload errors** occur when the Client Workload is incorrectly specified, preventing a match with an Access Policy.

**Solution** - Verify your workload configuration:

For Server Workload issues:

1. Check that the `--server-workload-host` value matches your configured Server Workload
2. Verify that the `--server-workload-port` value is correct

For Client Workload issues:

1. Check that the `--client-id` value matches your configured Client Workload
2. Ensure the Client Workload exists in your tenant

For both cases: 3. Use any workload IDs provided in error messages to cross-reference with your tenant data 4. Verify that you’ve configured your workloads correctly in your Access Policies

### Failed to match Credential Provider errors

[Section titled “Failed to match Credential Provider errors”](#failed-to-match-credential-provider-errors)

If you encounter an error like this:

```shell
Failed to match Credential Provider. Matched Client Workload ID: [client-id] and Server Workload ID: [server-id]
```

This error indicates that Aembit CLI successfully matched both a Client Workload and a Server Workload, but it couldn’t match the Credential Provider in the Access Policy.

**Solution** - Use the provided workload IDs to diagnose and fix the configuration:

1. Use the provided workload IDs to identify which Access Policy each workload belongs to
2. Ensure that you’ve configured both workloads in the same Access Policy
3. Verify that the Access Policy is active and configured correctly
4. Check that the Credential Provider in the Access Policy matches your expected credential type

### Invalid `client_id` error

[Section titled “Invalid client\_id error”](#invalid-client_id-error)

When using the `aembit credentials get` command, if you encounter an error like this:

```shell
Invalid client_id: Failed to obtain access token. HTTP response status: 400 Bad Request. Server error: invalid_client
```

This error indicates that the `--client-id` Aembit CLI received isn’t valid or doesn’t match any Edge SDK Client IDs in your Aembit Tenant.

**Solution** - Try the following:

* Ensure that you’ve formatted the Edge SDK Client ID correctly, as shown in the [credentials get](reference/credentials-get.md) documentation. You can find your Edge SDK Client ID in your Aembit Tenant by following the steps in [Find your Edge SDK Client ID](../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).
* Verify that the Edge SDK Client ID you set as the `--client-id` matches the Edge SDK Client ID of the Trust Provider in your Aembit Tenant.
* Check that the Trust Provider is correctly configured in your Aembit Tenant and that it’s a part of the Access Policy that applies to the Client Workload you’re trying get a credential for.

### Can’t connect to cloud error

[Section titled “Can’t connect to cloud error”](#cant-connect-to-cloud-error)

When using the `aembit credentials get` command, if you encounter an error like this:

```shell
Cannot connect to cloud: Error when sending OIDC client credentials request: \
error sending request for url (https://<teantId>.id.aembit.io/connect/token); client error (Connect); \
An existing connection was forcibly closed by the remote host. (os error 10054)
```

This error indicates that the Aembit CLI can’t connect to the Aembit cloud service, which is necessary for retrieving credentials.

**Solution** - Try the following:

* Ensure that you have a stable internet connection.
* Check if the Aembit cloud service is operational by visiting the [Aembit Status Page](https://status.aembit.io/).
* Verify that the `--client-id` you provided is correct and matches the Edge SDK Client ID in your Aembit Tenant. See the [credentials get](reference/credentials-get.md) documentation for the correct format.
* If you’re using a proxy or firewall, ensure that it allows connections to the Aembit cloud service.

### Environment variable contains escaped characters

[Section titled “Environment variable contains escaped characters”](#environment-variable-contains-escaped-characters)

When using the `aembit credentials get` command with the `--credential-names` option, you might notice that the environment variables set by the command contain escaped characters, such as quotes or newlines. This happens because when credentials contain special characters, Aembit CLI automatically escapes them to prevent shell command injection vulnerabilities or syntax errors.

For example, if you retrieve a credential that contains special characters, the Aembit CLI escapes the outputs like this:

```shell
aembit credentials get --client-id "$CLIENT_ID" \
  --id-token "$GITHUB_IDENTITY_TOKEN" \
  --server-workload-host pgsql.local \
  --server-workload-port 5432 \
  --credential-names USERNAME,PASSWORD


# Output is properly escaped:
export PASSWORD='t'\''his; is a\n test$SHELL'
```

**Solution** - To resolve this, use the `eval` command with these credentials, as Aembit CLI handles the escaping automatically.

```shell
eval $(aembit credentials get --client-id "$CLIENT_ID" \
  --id-token "$GITHUB_IDENTITY_TOKEN" \
  --server-workload-host pgsql.local \
  --server-workload-port 5432 \
  --credential-names USERNAME,PASSWORD)


# Output isn't escaped:
export MY_CREDENTIAL='this; is a\n test$SHELL'
```

### No Access Policy found error

[Section titled “No Access Policy found error”](#no-access-policy-found-error)

You might encounter an error message like:

```plaintext
Error matching access policy. Matched client workload ID: [client-id] and server workload ID: [server-id]
```

This occurs when you successfully match both a Client Workload and a Server Workload, but they’re attached to different Access Policies or to no Access Policy at all, preventing credential retrieval.

**Solution** - Use the provided workload IDs to diagnose and fix the configuration in your Aembit Tenant:

1. Use the provided workload IDs to identify which Access Policy each workload belongs to
2. Ensure that you’ve configured both workloads in the same Access Policy
3. Verify that the Access Policy is active and configured the way you expect
4. Check that the Credential Provider in the Access Policy matches your expected credential type

### Invalid `resource_set_id` error

[Section titled “Invalid resource\_set\_id error”](#invalid-resource_set_id-error)

When you encounter an error like this:

```shell
Invalid resource_set_id: Communication with your cloud tenant encountered an internal error.
```

This error indicates that the Aembit CLI is unable to communicate with your Aembit Tenant, possibly due to a misconfiguration or a temporary issue with the Aembit cloud service.

This can be due to a misconfiguration of your access policies. It can also be due to a software bug. Please consider these additional steps:

**Solution** - Try the following:

* Double-check your `resource_set_id` configuration in your Aembit Tenant.
* Ensure that the `resource_set_id` is correctly set in your Aembit CLI command.
* Double check that you’ve configured your Access Policy for the Resource Set you expect.
* Make sure that you’re using the [latest version of Aembit CLI](https://releases.aembit.io/agent/index.html).
* Verify that your Aembit Tenant is operational and that there are no ongoing issues with the Aembit cloud service. See the [Aembit Status Page](https://status.aembit.io/) for any reported outages or issues.
* If the issue persists, consider [Submitting a support request ](https://support.aembit.io/hc/en-us/articles/25007312326932-How-To-Submit-a-Support-Request) to Aembit Support for assistance, providing them with the error message and any relevant details about your configuration.

### (Windows only) PowerShell “running scripts is disabled on this system” errors

[Section titled “(Windows only) PowerShell “running scripts is disabled on this system” errors”](#windows-only-powershell-running-scripts-is-disabled-on-this-system-errors)

If you run the PowerShell script from the [Getting credentials on Windows](usage/get-credentials-windows.md) guide and encounter an error like this:

```powershell
PS C:\Users\aembit\Documents\aembit_agent_cli_windows_amd64_1.24.3328> .\get-credentials.ps1
.\test.ps1 : File C:\Users\aembit\Documents\aembit_agent_cli_windows_amd64_1.24.3328\test.ps1 cannot be loaded because
running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```

This error indicates that PowerShell’s execution policy is set to restrict script execution.

**Solution** - To resolve this, you can change the execution policy to allow script execution.

Open PowerShell as an administrator and run the following command:

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser


Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic at
https:/go.microsoft.com/fwlink/?LinkID=135170. Do you want to change the execution policy?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"): A
```

Answer “A” to allow all scripts to run.

Then, try running the PowerShell script again:

```powershell
.\get-credentials.ps1
Base64 encoded key: eW91ci0yNTYtYml0LXNlY3JldA==
OIDC token eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJodHRwczovLzRjMWI2MS5hZW1iaXQtZW5nLmNvbSIsImlzcyI6InNlbGYiLCJzdWIiOiJzZWxmIiwiZXhwIjoxNzU0MDc3MjIzLCJpYXQiOjE3NTQwNzM2MjN9.BjKDd7bIQmIAPDhUR2dUW04jrokBf5g2kfgynyeto3c
...
```
