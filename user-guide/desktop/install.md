---
type: how-to
title: "Install Aembit Desktop and sign in"
description: "Install Aembit Desktop on Windows 11, sign in through your Identity Provider, and route a tool to it."
resource: https://docs.aembit.io/user-guide/desktop/install/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Install Aembit Desktop and sign in

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Install Aembit Desktop on your Windows 11 machine, trust your Aembit Tenant’s certificate authority, sign in through your Identity Provider, and point a tool at the local proxy. Everything here happens on your own machine. An Aembit Tenant administrator does the other half in the Aembit Tenant UI, following [Set up user access for Aembit Desktop](set-up-user-access.md).

## Before you begin

Get these from your Aembit Tenant administrator before you start:

* **The Aembit Client ID.** Everyone who signs in through the same Identity Provider uses the same value, and you pass it to `adt login`. It identifies your Identity Provider for Aembit Desktop sign-in; a Client Workload‘s Aembit Client ID is a different value.

* **Your tenant ID.** It’s the first part of your Aembit Tenant UI address: the tenant ID in `https://abc123.aembit.io` is `abc123`.

* **The address of a service you can reach**, which the administrator configured as a Server Workload.

You also need a Windows 11 machine and permission to install software on it. The installer writes to `C:\Program Files\` and changes the system `PATH`, so it prompts for administrator approval.

## Install Aembit Desktop

1. Download the Windows installer from the [Aembit Desktop releases page](https://docs.aembit.io/releases/desktop/).

   The filename is `aembit_desktop_windows_amd64_<version>.msi`. The releases page lists the checksum beside the download and shows how to verify it.

2. Run the installer and accept the administrator prompt.

   The installer puts `adt.exe` in `C:\Program Files\Aembit\Desktop\` for every account on the machine and adds that directory to the system `PATH`. Installing a newer version over an existing one stops Aembit Desktop first, so run `adt start` again after an upgrade.

3. Open a new terminal and confirm that `adt` is on your `PATH`:

   ```powershell
   adt --version
   ```

   The terminal must be one you opened after the install finished, because an existing terminal holds the `PATH` it started with.

## Trust the Aembit Tenant CA certificate

Aembit Desktop terminates TLS on its way through the local proxy, using a certificate issued under your Aembit Tenant’s certificate authority. When the Client Workload has a [standalone certificate authority](../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt-standalone-ca.md), Aembit Cloud issues the certificate under that one instead. Your machine rejects those connections until it trusts that certificate authority.

1. Write the certificate to a file, substituting your own tenant ID:

   ```powershell
   adt get-tenant-ca-cert --tenant-id abc123 --file aembit-tenant-ca.crt
   ```

   `adt` saves the certificate and installs nothing. It also prints a `certutil` command that trusts the certificate for your Windows user only, which the next step replaces with a machine-wide import.

2. Add the certificate to the machine’s trusted root store from an elevated PowerShell prompt:

   ```powershell
   Import-Certificate -FilePath .\aembit-tenant-ca.crt -CertStoreLocation Cert:\LocalMachine\Root
   ```

   Tools that keep their own trust store, such as some language runtimes and container images, need the certificate added there as well.

3. Close the elevated prompt, and run the rest of the `adt` commands from a regular PowerShell window. Background services started from an elevated prompt run with administrator privileges.

## Start Aembit Desktop

```powershell
adt start
```

`adt start` launches two background services: one that holds your session credentials and one that runs the proxy on `127.0.0.1`, port 8000 unless your administrator changed it. It prints the address the proxy listens on. Both keep running after you close the terminal. Nothing starts them again after the machine restarts, so run `adt start` after each restart.

Start Aembit Desktop before you set `HTTP_PROXY` or `HTTPS_PROXY` for your tools. The proxy service refuses to start when either variable already points at its own address, because that would send its own outbound traffic back to itself. For a traffic type whose variable is unset or empty, it checks the Windows manual proxy setting the same way.

## Sign in

1. Run `adt login` with the Aembit Client ID your administrator gave you:

   ```powershell
   adt login --client-id <aembit-client-id>
   ```

   When your administrator delivered the Aembit Client ID in a settings file, run `adt login` without `--client-id`. `adt` prints the authorization URL and opens it in your default browser. Pass `--no-browser` to have it print the URL without opening anything, then open the URL yourself.

2. Sign in at your Identity Provider in the browser, under whatever rules your organization already enforces.

   `adt` prints `Login successful.` once it stores the session.

Your session lasts as long as your administrator configured. The credential service renews your tokens on a schedule while it runs. The session ends early when the credential service goes a full Token Lifetime without renewing, for example because your machine was off or you ran `adt stop`. It ends at the Absolute Token Lifetime however often it renewed. Either way, you run `adt login` again.

The credential service also stops renewing after a renewal fails, for example because your machine had no network connection at that moment. Run `adt stop` and then `adt start` to retry the renewal, or run `adt login` if that doesn’t restore access.

## Route a tool through the proxy

Aembit Desktop intercepts nothing on its own. After `adt start` has run, point each tool at the local proxy in one of these ways:

* **`adt beta shell`** opens a new shell with `HTTP_PROXY` and `HTTPS_PROXY` set to the local proxy. When the `SHELL` environment variable names a program other than PowerShell, as Git Bash and Cygwin set it, it opens that program; otherwise it opens PowerShell 7 or Windows PowerShell. Windows PowerShell 5.1 doesn’t send `Invoke-WebRequest` or `Invoke-RestMethod` requests through the proxy those variables name, because those commands read proxy environment variables only from PowerShell 7.0 on. Started from Command Prompt, `adt beta shell` opens Windows PowerShell 5.1, so start it from PowerShell 7 to use those commands.

* **`adt beta env`** prints the commands that set the same variables, choosing the syntax the same way. Pass `--pwsh` or `--sh` to choose PowerShell or Portable Operating System Interface (POSIX) shell syntax instead, for example `--pwsh` in a PowerShell session that inherited `SHELL` from Git Bash. It has no Command Prompt syntax, so run it from PowerShell, Git Bash, or Cygwin. To apply them to the current PowerShell session:

  ```powershell
  adt beta env --pwsh | Invoke-Expression
  ```

* **The tool’s own proxy setting, or the variables set by hand**, with the port `adt start` printed in place of `8000` when your administrator changed it:

  ```powershell
  $env:HTTP_PROXY = "http://127.0.0.1:8000"
  $env:HTTPS_PROXY = "http://127.0.0.1:8000"
  ```

Both `adt beta` commands read the port from the running proxy and fail when `adt start` hasn’t run. They read it once, so after the port changes, open a new `adt beta shell` or run `adt beta env` again. Neither sets `NO_PROXY`, so set it yourself for any host that must bypass the proxy. Tools that pin TLS certificates reject the proxy’s certificate whatever you configure, so route those around the proxy with `NO_PROXY`.

Don’t run `adt start` from a shell that already points at the local proxy, such as one `adt beta shell` opened. The proxy service refuses to start there, because its own outbound traffic would come back to itself.

## Verify access

Replace `8000` with the port `adt start` printed if your administrator changed it.

```powershell
curl.exe --proxy "http://127.0.0.1:8000" "https://<server-workload-address>"
```

Type `curl.exe`, with the extension. In Windows PowerShell, `curl` is an alias for `Invoke-WebRequest`, which takes different options and ignores the `--proxy` flag.

Test against a service that rejects requests without a valid credential. A successful response from such a service means the request reached it with an Aembit-brokered credential attached, because you never supplied one. A service that accepts requests without a credential can respond even when no Client Workload matches you, because the proxy then passes the HTTPS connection through without injecting anything. A rejection can instead come from the service, the target URL, the network, or TLS certificate validation. Ask your administrator to use Access Authorization Events to determine whether Aembit denied the request. They can then confirm that an active Access Policy and its components match the request.

Run `adt status` at any time to see whether the services are running and whether you hold a current session. Add `--verbose` for process IDs and credential storage detail.

## Stop and sign out

```powershell
adt logout
```

`adt logout` discards the stored session, and the next `adt login` sends you back to your Identity Provider. Run it before `adt stop`, because it reaches the credential service to clear what that service holds.

```powershell
adt stop
```

`adt stop` stops both background services. It leaves your session in place, so `adt start` after a plain `adt stop` picks up where you left off.

## Troubleshooting

When a command fails or a tool can’t reach a service, see [Troubleshoot Aembit Desktop](troubleshooting.md).

## Next steps

* [Aembit Desktop reference](reference.md) - Every `adt` command and option, the settings, the environment variables, and the defaults and limits
* [Aembit Desktop concepts](concepts.md) - What the two background services do and how a credential reaches a request
* [What Aembit Desktop supports](reference.md#supported-platforms-and-protocols) - The platforms, protocols, and sign-in methods Aembit Desktop supports
