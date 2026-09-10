---
type: troubleshooting
title: "Troubleshooting Aembit CLI"
description: "A guide to troubleshooting common issues with Aembit CLI"
resource: https://docs.aembit.io/dev-guide/cli/troubleshooting/
interface: cli
tags: ["cli"]
timestamp: 2026-09-09T09:33:20-07:00
---

# Troubleshooting Aembit CLI

Aembit CLI is a powerful tool, but you might encounter some common issues while using it. These topics provide solutions to those issues, helping you troubleshoot problems you may encounter.

## Common errors and solutions

The following are common errors you might encounter when using the Aembit CLI, along with their solutions:

### No output when using `--credential-names`

When you run the command that includes the `--credential-names` flag, such as:

```shell
eval $(aembit credentials get --client-id <clientId> \
  --server-workload-host <host> \
  --server-workload-port <port> \
  --credential-names "USERNAME,PASSWORD")
```

You might notice that there is no output in the terminal, even though the command succeeded.

Aembit CLI writes one `export` statement per credential to standard output, and `eval` consumes that output and runs it as a shell command instead of printing it. The credentials are now environment variables in the shell that ran `eval`, which is why the terminal shows nothing.

**Solution** - Use the `echo` command to verify that the shell holds the values, and prefix each name with `$` so the shell expands the variable rather than printing the name:

```shell
echo $USERNAME
echo $PASSWORD
```

The shell prints the values:

```text
your-username-value
your-password-value
```

To see the statements themselves, run the command without `eval`:

```shell
aembit credentials get --client-id <clientId> \
  --server-workload-host <host> \
  --server-workload-port <port> \
  --credential-names "USERNAME,PASSWORD"
```

The command prints one `export` statement per credential:

```text
export USERNAME='your-username-value'
export PASSWORD='your-password-value'
```

### `TOKEN` Credential mismatch errors

When running the `aembit credentials get` command without specifying `--credential-names`, you might encounter an error message like this:

```text
Credential(s) not returned by tenant: TOKEN
```

This occurs when you’re requesting the default `TOKEN` credential but the matched Access Policy provides username/password credentials instead of a token.

The same error names whichever credential your Aembit Tenant couldn’t resolve. You also see it when you rename a credential that its Credential Provider doesn’t allow you to rename:

```text
Credential(s) not returned by tenant: MY_TOKEN
```

Only some Credential Providers accept a name of your choosing, and the rest recognize a fixed set of names. See [Credential name support](credential-names.md) for the names they recognize and for how to use a different name for a credential.

**Solution** - Specify the correct credential names that match what your Access Policy expects for the credential you want to retrieve.

For example, if your Access Policy provides `USERNAME` and `PASSWORD` credentials, you should use the `--credential-names` flag to specify those:

```shell
aembit credentials get --client-id <clientId> \
  --server-workload-host <host> \
  --server-workload-port <port> \
  --credential-names "USERNAME,PASSWORD"
```

If you use or plan to use token-based authentication, check your Access Policy’s configuration in your Aembit Tenant. Confirm it has a matching OIDC token Trust Provider.

### Access Policy matching errors

Aembit CLI reports every Access Policy matching failure with the same message, assembled from up to four segments:

```text
Error matching access policy. <reason>. Matched client workload ID: <client-workload-id>. Matched server workload ID: <server-workload-id>.
```

Only `Error matching access policy.` always appears. Aembit CLI adds `<reason>` when your tenant supplies one, then adds each `Matched ... ID` segment only for the workload it identified. When Aembit identifies neither workload, the message ends after the reason.

Read the reason segment first, because it tells you which part of your configuration to check.

| Reason                                                               | What it means                                                       | Where to start                                                                                                                    |
| -------------------------------------------------------------------- | ------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `Failed to identify Client Workload`                                 | No Client Workload matched the attestation data Aembit CLI sent.    | Confirm `--client-id` is the right Trust Provider, then see [Client Workload isn’t identified](#client-workload-isnt-identified). |
| `Failed to identify Server Workload`                                 | No Server Workload matched the host and port you requested.         | Check `--server-workload-host` and `--server-workload-port` against the Server Workload in your tenant.                           |
| `No Access Policy found`                                             | Aembit identified both workloads, but no Access Policy joins them.  | Add both workloads to the same Access Policy, and confirm that policy is active.                                                  |
| `Multiple Access Policy found`                                       | More than one Access Policy matched, so Aembit can’t choose one.    | Remove the overlap so a single Access Policy covers this pair of workloads.                                                       |
| `Access policy has multiple credential providers of different types` | The matched Access Policy maps Credential Providers of mixed types. | Change the policy so every Credential Provider it maps has the same type.                                                         |

**Solution** - Fix what the reason names, then use the reported IDs to look each workload up in your tenant and confirm you edited the objects you expected.

> **The reported ID isn’t the value you pass back to the CLI**
>
> The client workload ID in these messages is the Client Workload’s resource ID, which Aembit exposes as `externalId` in the API. Use it to look the workload up in your tenant. Don’t pass it to `--client-workload-id`, which takes the workload’s **Aembit Client ID** instead. Both values use the same UUID format.
>
> For a side-by-side comparison of all three identifiers, see the [Aembit identifier reference](../../reference/identifiers.md).

#### Client Workload isn’t identified

A `Failed to identify Client Workload` reason means no Client Workload matched the attestation data.

1. Check that the `--client-id` value is the Edge SDK Client ID for the Trust Provider you expect to attest this workload
2. Ensure the Client Workload exists in your tenant
3. If the workload uses the **Aembit Client ID** Client Identification method, pass [`--client-workload-id`](reference/credentials-get.md#--client-workload-id) with its Aembit Client ID

Aembit matches an Aembit Client ID only against that identifier type. A workload identified solely by that method never matches unless you pass the value.

### Credential Provider errors

A Credential Provider problem reaches you in one of two forms, depending on where it occurs.

If the matched Access Policy maps Credential Providers of more than one type, Aembit reports it as an [Access Policy matching error](#access-policy-matching-errors) with a reason segment:

```text
Error matching access policy. Access policy has multiple credential providers of different types. Matched client workload ID: [client-workload-id]. Matched server workload ID: [server-workload-id].
```

If Aembit can’t supply the credential itself, Aembit CLI reports a generic tenant error instead, with no workload IDs:

```text
Communication with your cloud tenant encountered an internal error. This can be due to a misconfiguration of your access policies. It can also be due to a software bug. Please consider these additional steps:
  - Double check each part of your access policy configuration.
  - Ensure you're using the most up-to-date release.
  - Check the cloud service status.
  - Run-run the same command with debug logging for additional details.
```

Because this message carries no IDs, it can’t tell you which workload or policy caused it.

**Solution** - Try the following:

1. Confirm both workloads belong to the same active Access Policy
2. Check that the Credential Provider in that policy supplies the credential type your Server Workload expects
3. Re-run the command with `--log-level debug` for the underlying error
4. Check the [Aembit Status Page](https://status.aembit.io/) for an ongoing incident

### Invalid `client_id` error

When using the `aembit credentials get` command, if you encounter an error like this:

```shell
Invalid client_id: Failed to obtain access token. HTTP response status: 400 Bad Request. Server error: invalid_client
```

This error indicates that the `--client-id` Aembit CLI received isn’t valid or doesn’t match any Edge SDK Client IDs in your Aembit Tenant.

**Solution** - Try the following:

* Ensure that you’ve formatted the Edge SDK Client ID correctly, as shown in the [credentials get](reference/credentials-get.md) documentation. You can find your Edge SDK Client ID in your Aembit Tenant by following the steps in [Find your Edge SDK Client ID](../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md).
* Verify that the Edge SDK Client ID you set as the `--client-id` matches the Edge SDK Client ID of the Trust Provider in your Aembit Tenant.
* Check that the Trust Provider is correctly configured in your Aembit Tenant and that it’s a part of the Access Policy that applies to the Client Workload you’re trying get a credential for.

### Can’t connect to cloud error

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

### DNS lookup fails with `failed to lookup address information`

On some Linux hosts, `aembit credentials get` can’t resolve your Aembit Tenant’s hostname and reports an error like this, while `dig` and `curl` on the same host resolve the same name:

```shell
Cannot connect to cloud: Error when sending OIDC client credentials request: error sending request for url (https://<tenantId>.id.aembit.io/connect/token); client error (Connect); dns error; failed to lookup address information: Try again
```

The default Linux build of Aembit CLI links statically against the musl C library, and the musl resolver treats some DNS responses as failures that the glibc resolver behind `dig` and `curl` tolerates. The case Aembit has seen is a resolver that answers the IPv6 (`AAAA`) query for the hostname with `REFUSED` while it answers the IPv4 (`A`) query normally. musl fails the whole lookup, and glibc uses the `A` answer.

**Solution** - Configure the resolver to answer an `AAAA` query for a name with no IPv6 address with an empty `NOERROR` response, which is what the DNS specification requires. That change fixes every client on the host.

If you can’t change the resolver, download the glibc build of Aembit CLI, `aembit_agent_cli_linux_amd64_glibc_<version>.tar.gz`, which uses the glibc resolver. The glibc build is amd64 only and requires glibc 2.28 or newer on the host. See [Choose a Linux build](usage/setup.md#choose-a-linux-build).

### Environment variable contains escaped characters

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

### Invalid `resource_set_id` error

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

If you run the PowerShell script from the [Getting credentials on Windows](usage/get-credentials-windows.md) guide and encounter an error like this:

```powershell
PS C:\Users\aembit\Documents\aembit_agent_cli_windows_amd64_1.24.3328> .\get-credentials.ps1
.\test.ps1 : File C:\Users\aembit\Documents\aembit_agent_cli_windows_amd64_1.24.3328\test.ps1 cannot be loaded because
running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```

This error indicates that PowerShell’s execution policy restricts script execution.

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
