---
type: troubleshooting
title: "Troubleshoot Aembit Desktop"
description: "Fix Aembit Desktop start, sign-in, proxy loop, settings file, and certificate errors on Windows 11."
resource: https://docs.aembit.io/user-guide/desktop/troubleshooting/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Troubleshoot Aembit Desktop

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Each section names what you see when Aembit Desktop fails on a Windows 11 machine, why it happens, and how to fix it. When none of them matches, [collect the log files](#collect-the-log-files) and send them to your Aembit Tenant administrator or to [Aembit support](https://aembit.io/contact).

## `adt start` reports an infinite loop

`adt start` fails with `Unable to start the HTTP proxy`, and the error under it ends with `This would cause an infinite loop.`

`HTTP_PROXY` or `HTTPS_PROXY` points at the local proxy in the shell where you ran `adt start`. The proxy service sends its own outbound traffic through the proxy those variables name, so it refuses to start when they name itself. A shell that `adt beta shell` opened, or one where you ran the output of `adt beta env`, has these variables set.

To fix it, run `adt start` from a shell where the variables are unset or name your organization’s gateway:

* In a shell that `adt beta shell` opened, type `exit` and run `adt start` from the shell you started in.

* In any other PowerShell session, clear the variables, then run `adt start`:

  ```powershell
  Remove-Item Env:HTTP_PROXY, Env:HTTPS_PROXY -ErrorAction SilentlyContinue
  adt start
  ```

For a traffic type whose variable is unset, the check reads the Windows manual proxy setting instead. If **Settings** > **Network & internet** > **Proxy** sets a manual proxy at the local proxy’s address, turn that proxy off and leave it off, then run `adt start`. The proxy service reads the Windows manual proxy setting again for every request, so turning the manual proxy back on sends its traffic back to itself.

## A command reports `Unable to read` a settings file

`adt start`, `adt login`, or `adt get-tenant-ca-cert` fails with `Error: Unable to read <path>`, followed by a `Caused by:` line.

The file at `<path>` exists but isn’t valid JSON, holds a value of the wrong type, or Aembit Desktop can’t read it. A settings file that doesn’t exist causes no error. A value outside its allowed range causes a different error that states the allowed range, such as `Invalid HTTP proxy port: Must be between 1 and 65535`. An unrecognized `log_level` causes `Invalid log level '<value>'`.

Correct the problem that the `Caused by:` line describes, in the contents of the file at `<path>` or in its permissions. A file under `C:\Program Files\Aembit\Desktop\Config\` belongs to your administrator, so ask them to fix it. [Configure Aembit Desktop settings](configure.md) shows the file format.

## A settings change has no effect

Aembit Desktop keeps behaving as it did before you changed a settings file. Check these causes in order:

* **The background services haven’t restarted.** They read the settings when they start. Run `adt stop` and then `adt start`.
* **The key name has a typo.** Aembit Desktop ignores a key it doesn’t recognize and reports no error. Compare the name with the [settings keys](reference.md#settings-keys).
* **A higher layer sets the same value.** `ManagedSettings.json` overrides everything, and a command option or an environment variable overrides your own `Settings.json`. See [Choose a settings file](configure.md#choose-a-settings-file) for the full order.

## `adt login` rejects the client ID

`adt login` fails with one of these messages:

* `You need to specify a login client ID.` means no client ID reached `adt login`. Pass `--client-id`, or ask your administrator to deliver it in a settings file.
* An error that starts with `Failed to parse client ID` means the value isn’t a well-formed Aembit client ID. Use the **Aembit Client ID** from the **Users** tab of your Identity Provider instead.
* `You specified a client workload that is unsuitable for login.` means the value is a well-formed client ID that isn’t for signing in a person, such as a Trust Provider‘s Edge SDK Client ID. Use the **Aembit Client ID** from the **Users** tab of your Identity Provider instead.

## Tools stop reaching services

Tools that worked earlier now fail.

* **The machine restarted.** Nothing starts Aembit Desktop after a restart. Run `adt start`.
* **You upgraded Aembit Desktop.** The installer stops the background services and doesn’t start them again. Run `adt start`.
* **Your session ended.** Run `adt login`. The credential service hands the new session to the proxy service, without a restart.
* **A renewal failed.** The credential service stops renewing after a renewal fails, and waits for you to sign in. `credential-daemon.log` then contains `Failed to handle current credential refresh state, switching to the Unauthenticated state`. Run `adt stop` and then `adt start`, which retries the renewal with your stored session, or run `adt login` if that doesn’t restore access.

Run `adt status` to see whether the services are running and whether you hold a current session.

## A tool rejects the proxy’s certificate

A tool fails with a certificate or TLS error when it connects through the local proxy.

To read an HTTPS request, the local proxy presents a certificate that Aembit Cloud issues under your Aembit Tenant’s certificate authority. When the Client Workload has a standalone certificate authority, Aembit Cloud issues it under that one instead. The tool doesn’t trust that certificate authority yet.

* Add the Aembit Tenant CA certificate to the machine’s trusted root store, as described in [Install Aembit Desktop and sign in](install.md#trust-the-aembit-tenant-ca-certificate). When the Client Workload has a [standalone certificate authority](../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md), trust that certificate authority instead.
* A tool that reads its own trust store instead of the Windows trusted root store needs the certificate added to that trust store.
* A tool that pins certificates accepts only the certificates it pinned, so it rejects the proxy’s certificate. Route it around the proxy with `NO_PROXY`, which also means Aembit Desktop injects no credential into its requests.

## The AWS CLI reports `CERTIFICATE_VERIFY_FAILED`

`adt beta tool aws` runs, and the AWS CLI fails with `CERTIFICATE_VERIFY_FAILED`.

`adt beta tool aws` points `AWS_CA_BUNDLE` at your Aembit Tenant CA certificate, so the AWS CLI trusts only that certificate. The AWS CLI rejects any other certificate, which happens in these cases:

* **No Client Workload matches your sign-in.** The local proxy passes the HTTPS connection through without reading it, and the AWS CLI receives AWS’s own certificate.
* **Aembit Cloud doesn’t tell the proxy to decrypt the connection.** The local proxy passes it through the same way.
* **The Client Workload has a standalone certificate authority.** The local proxy presents a certificate issued under it rather than under your Aembit Tenant CA.
* **The saved copy of your Aembit Tenant CA certificate is out of date.** `adt beta tool aws` downloads the certificate once, saves it in `%USERPROFILE%\AppData\Local\AembitDesktop\Config\`, and reuses that file after your Aembit Tenant CA changes. The filename starts with `ca_root_` and ends with your tenant ID and stack domain, with each dot replaced by an underscore, such as `ca_root_abc123_useast2_aembit_io.pem`. Delete the file and run the command again, which downloads the current certificate.

Ask your administrator to check each case for your sign-in and the AWS endpoint the command calls. [Use the AWS CLI with Aembit Desktop](aws-cli.md) lists the rest of the setup.

## Reset Aembit Desktop

When nothing else restores Aembit Desktop, reset your own copy of it:

```powershell
adt pristine
```

`adt pristine` stops the background services and lists what it’s about to remove, then asks `Continue?`. When you confirm, it deletes the encryption key for your stored session from Windows Credential Manager, then your own settings and log folders. It keeps the system settings files your administrator placed. Collect the log files first if you plan to send them to support, because `adt pristine` removes them.

Afterward, run `adt start` and `adt login` again.

## Collect the log files

Each background service writes a log file under `%USERPROFILE%\AppData\Local\AembitDesktop\Logs\`:

* `credential-daemon.log` for the credential service, which covers signing in and renewing the session.
* `proxy-daemon.log` for the proxy service, which covers the traffic your tools send to the local proxy.

Send both files to your Aembit Tenant administrator or to [Aembit support](https://aembit.io/contact). To capture more detail, set `log_level` to `debug` in your own `Settings.json`, or set `AEMBIT_LOG_LEVEL` in your shell. Then run `adt stop` and `adt start` from that shell, and reproduce the problem.
