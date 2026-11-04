---
type: reference
title: "Aembit Desktop reference"
description: "The adt commands, options, settings, environment variables, defaults, and limits of Aembit Desktop."
resource: https://docs.aembit.io/user-guide/desktop/reference/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Aembit Desktop reference

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

`adt` is the command you use to run Aembit Desktop on a Windows 11 machine. This page lists everything it accepts and the values it uses when you tell it nothing. [Install Aembit Desktop and sign in](install.md) walks through the commands in the order you first need them.

## Commands

| Command              | What it does                                                                                      |
| -------------------- | ------------------------------------------------------------------------------------------------- |
| `login`              | Signs you in to your Aembit Tenant through your Identity Provider and stores the session.         |
| `logout`             | Clears the stored session. The credential service must be running.                                |
| `start`              | Starts the credential service and the proxy service, and prints the address the proxy listens on. |
| `status`             | Reports whether each service is running and whether you hold a current session.                   |
| `stop`               | Stops both services and leaves the stored session in place.                                       |
| `get-tenant-ca-cert` | Writes your Aembit Tenant’s root certificate authority certificate to standard output or a file.  |
| `pristine`           | Stops both services and removes your settings, logs, stored session, and its encryption key.      |
| `beta shell`         | Opens a shell whose proxy variables point at the local proxy.                                     |
| `beta env`           | Prints the commands that point a shell’s proxy variables at the local proxy.                      |
| `beta tool aws`      | Runs the AWS CLI through the local proxy.                                                         |

### `adt login`

```powershell
adt login --client-id <aembit-client-id>
```

| Option             | What it does                                                                                                                                                                                                  |
| ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--client-id <id>` | The **Aembit Client ID** from the **Users** tab of your Identity Provider in the Aembit Tenant UI. Required unless `AEMBIT_DESKTOP_USER_LOGIN_CLIENT_ID` or the `tenant.login_client_id` setting supplies it. |
| `--no-browser`     | Prints the authorization URL without opening a browser. Open the URL yourself to finish signing in.                                                                                                           |

`adt login` starts the credential service if it’s not already running, so you can sign in without running `adt start` first. It fails with an error that starts with `Failed to parse client ID` when the value isn’t a well-formed Aembit client ID. It fails with `You specified a client workload that is unsuitable for login.` when the value is a well-formed client ID that isn’t for signing in a person, such as a Trust Provider’s Edge SDK Client ID.

### `adt get-tenant-ca-cert`

```powershell
adt get-tenant-ca-cert --tenant-id <tenant-id> --file aembit-tenant-ca.crt
```

| Option              | What it does                                                                                                                                                                                                                                                                                               |
| ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `--tenant-id <id>`  | Your Aembit Tenant’s ID, which is the first part of your Aembit Tenant UI address. Without it, `AEMBIT_TENANT_ID`, or the `tenant.id` setting, `adt` uses the Aembit Tenant of your current sign-in, and fails with `Missing tenant ID` when the credential service isn’t running or you aren’t signed in. |
| `--file <path>`     | Writes the certificate to this file instead of standard output.                                                                                                                                                                                                                                            |
| `--overwrite`       | Replaces the file named by `--file` when it already exists. Without it, `adt` leaves an existing file alone and fails.                                                                                                                                                                                     |
| `--format <format>` | `pem` or `der`. Overrides the format the file extension implies.                                                                                                                                                                                                                                           |

Without `--format`, a file ending in `.cer` or `.der` gets the Distinguished Encoding Rules (DER) format, and any other file gets Privacy-Enhanced Mail (PEM) format. `adt` refuses to write DER to a terminal; name a file or use PEM. On Windows, `adt` also prints a `certutil` command that adds the saved certificate to your Windows user’s trusted root store. It installs nothing itself.

### `adt pristine`

```powershell
adt pristine
```

`adt pristine` stops both services, lists what it’s about to remove, and asks `Continue?` before it removes anything. It deletes the encryption key for your stored session from Windows Credential Manager, then your own settings and log folders. It keeps the system settings files your administrator placed. It has no option to skip the confirmation. Run `adt login` again afterward.

### `adt beta shell` and `adt beta env`

```powershell
adt beta shell
adt beta env --pwsh | Invoke-Expression
```

`adt beta shell` opens a new shell with `HTTP_PROXY` and `HTTPS_PROXY` set to the local proxy, at `http://127.0.0.1:<port>`. When the `SHELL` environment variable names a program other than PowerShell, it opens that program as a Portable Operating System Interface (POSIX) shell. Otherwise it opens PowerShell 7 or Windows PowerShell, depending on `PSModulePath`. Windows PowerShell 5.1 doesn’t send `Invoke-WebRequest` or `Invoke-RestMethod` requests through the proxy those variables name, because those commands read proxy environment variables only from PowerShell 7.0 on. Started from Command Prompt, `adt beta shell` opens Windows PowerShell 5.1, so start it from PowerShell 7 to use those commands.

`adt beta env` prints the commands that set the same variables instead of opening a shell, in the syntax of the shell it detects the same way. In PowerShell it prints `HTTP_PROXY` and `HTTPS_PROXY` only. It has no Command Prompt syntax, so in Command Prompt it prints PowerShell lines that Command Prompt can’t run.

| Option   | What it does                                                                               |
| -------- | ------------------------------------------------------------------------------------------ |
| `--pwsh` | Prints PowerShell syntax whatever shell it detects.                                        |
| `--sh`   | Prints Portable Operating System Interface (POSIX) shell syntax whatever shell it detects. |

Both commands read the port from the running proxy service, so `adt start` must have run. They read it once, when they run, so after the port changes, open a new shell or run `adt beta env` again. Neither sets `NO_PROXY`.

### `adt beta tool aws`

```powershell
adt beta tool aws [--dry-run] <aws-cli-arguments>
```

`adt beta tool aws` runs `aws` with these environment variables set, and passes everything after it to the AWS CLI:

| Variable                                     | Value                                                                                          |
| -------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| `HTTPS_PROXY`                                | The local proxy, `http://127.0.0.1:<port>`.                                                    |
| `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY` | `aembit-placeholder`. The proxy replaces these with the credential the Access Policy supplies. |
| `AWS_CA_BUNDLE`                              | A copy of your Aembit Tenant CA certificate that `adt` saves in your settings folder.          |

| Option      | What it does                                                                                                 |
| ----------- | ------------------------------------------------------------------------------------------------------------ |
| `--dry-run` | Prints the environment variables and the command instead of running it. Put it before the AWS CLI arguments. |

It exits with the exit code of the AWS CLI, or with `1` when it can’t start `aws`. It needs the services running and a current session. [Use the AWS CLI with Aembit Desktop](aws-cli.md) walks through it.

### Verbose output on `adt status` and `adt stop`

| Option            | What it does                                                                                    |
| ----------------- | ----------------------------------------------------------------------------------------------- |
| `-v`, `--verbose` | On `status`, adds process IDs and credential storage detail. On `stop`, adds diagnostic detail. |

## Global options

| Option      | What it does                                           |
| ----------- | ------------------------------------------------------ |
| `--help`    | Prints usage for `adt` or for the subcommand you name. |
| `--version` | Prints the installed Aembit Desktop build.             |

## Settings

Aembit Desktop reads settings from JSON files, environment variables, and command options. [Configure Aembit Desktop settings](configure.md) covers the order they apply in and how to deliver the files.

### Settings files

| File                   | Location                                            | Who places it                                           |
| ---------------------- | --------------------------------------------------- | ------------------------------------------------------- |
| `ManagedSettings.json` | `C:\Program Files\Aembit\Desktop\Config\`           | An administrator. Overrides every other layer.          |
| `DefaultSettings.json` | `C:\Program Files\Aembit\Desktop\Config\`           | An administrator. Overrides only the built-in defaults. |
| `Settings.json`        | `%USERPROFILE%\AppData\Local\AembitDesktop\Config\` | The person who uses the machine.                        |

The installer creates none of these files.

### Settings keys

| Key                      | Type    | Default             | Environment variable                  | What it sets                                                                                            |
| ------------------------ | ------- | ------------------- | ------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| `log_level`              | string  | `info`              | `AEMBIT_LOG_LEVEL`                    | How much the background services log: `off`, `trace`, `debug`, `info`, `warn`, or `error`, in any case. |
| `proxy.http_port`        | integer | `8000`              | `AEMBIT_PROXY_HTTP_PORT`              | The port the local proxy listens on, from 1 to 65535.                                                   |
| `tenant.login_client_id` | string  | none                | `AEMBIT_DESKTOP_USER_LOGIN_CLIENT_ID` | The Aembit Client ID that `adt login` uses.                                                             |
| `tenant.id`              | string  | none                | `AEMBIT_TENANT_ID`                    | The tenant ID that `adt get-tenant-ca-cert` uses.                                                       |
| `tenant.stack_domain`    | string  | `useast2.aembit.io` | `AEMBIT_STACK_DOMAIN`                 | The domain of the Aembit stack your Aembit Tenant runs in.                                              |

A key name in a file is a path through nested JSON objects: `proxy.http_port` is `{"proxy": {"http_port": 8000}}`. Aembit Desktop ignores a key it doesn’t recognize.

## Environment variables

Each variable in the [settings keys](#settings-keys) table sets its setting, and wins over the settings files except `ManagedSettings.json`. The proxy service also reads the proxy variables your other tools read. For a traffic type whose variable is unset, it uses the Windows manual proxy setting instead, and it reads that setting again for every request.

| Variable                    | What Aembit Desktop does with it                                                                                                                                                                                                                                                                 |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `HTTP_PROXY`, `HTTPS_PROXY` | Sends its own outbound traffic through the proxy named there, so Aembit Desktop works behind a secure web gateway. The proxy service refuses to start when either variable resolves to its own address. For an unset or empty variable, it checks the Windows manual proxy setting the same way. |
| `NO_PROXY`                  | Excludes the hosts you list from the upstream proxy. When it’s unset, the bypass list of the Windows manual proxy setting applies instead.                                                                                                                                                       |

Start Aembit Desktop before you point `HTTP_PROXY` and `HTTPS_PROXY` at the local proxy for your tools, so the proxy service reads your organization’s upstream proxy rather than its own address.

## Defaults and limits

| Setting                         | Value                              | Notes                                                                                                      |
| ------------------------------- | ---------------------------------- | ---------------------------------------------------------------------------------------------------------- |
| Proxy address                   | `127.0.0.1:8000`                   | The address is always `127.0.0.1`. Change the port with `proxy.http_port`.                                 |
| Idle connection timeout         | 1 hour                             | The proxy closes a persistent HTTP/1.1 connection after an hour with no requests. Not configurable.        |
| Install location                | `C:\Program Files\Aembit\Desktop\` | Added to the system `PATH` by the installer.                                                               |
| Token Lifetime default          | 24 hours                           | How long each access token lasts and how long a session can go without renewing. The minimum is 5 minutes. |
| Absolute Token Lifetime default | 30 days                            | The longest a session can last. The maximum is 365 days.                                                   |

Your administrator sets both lifetimes on the **Users** tab of the Identity Provider in the Aembit Tenant UI, covered in [Set up user access for Aembit Desktop](set-up-user-access.md).

## Supported platforms and protocols

Aembit Desktop supports:

* **Windows 11 on x64.** The public beta ships only a 64-bit Windows installer, and no other operating system has one.
* **HTTP and HTTPS traffic**, through the local proxy at `127.0.0.1`, on port 8000 unless you change it.
* **Sign-in through an OIDC 1.0 or SAML 2.0 Identity Provider you configure.** An Aembit Tenant that already uses an Identity Provider for Aembit Tenant UI Single Sign-On reuses that Identity Provider.
* **The default Resource Set only.** Aembit Desktop doesn’t support any other Resource Set, so create every component of an Aembit Desktop Access Policy in the default Resource Set.

Aembit Desktop doesn’t support:

* **Protocols other than HTTP and HTTPS.** Database, SSH, and other traffic doesn’t pass through the local proxy.
* **Transparent traffic interception.** Every tool that uses Aembit Desktop points at the local proxy explicitly, either through its own proxy setting or through the `HTTP_PROXY` and `HTTPS_PROXY` environment variables.
* **Applications that pin TLS certificates.** The local proxy terminates TLS with a certificate issued under your Aembit Tenant CA, or under the Client Workload’s standalone certificate authority, so a pinned application rejects the connection. Route these applications around the proxy.
* **Signing in without single sign-on.** Every person who uses Aembit Desktop signs in through your Identity Provider.
* **Starting after a restart.** Nothing starts Aembit Desktop when the machine restarts, so the person runs `adt start` again.
* **Telling local processes apart.** The local proxy doesn’t identify which process or Windows account opened a connection. Any process on the machine that connects to it reaches every service the signed-in person’s Access Policies allow.
