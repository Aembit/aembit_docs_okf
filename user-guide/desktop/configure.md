---
type: how-to
title: "Configure Aembit Desktop settings"
description: "Deliver Aembit Desktop settings to Windows 11 machines through system, managed, and user settings files."
resource: https://docs.aembit.io/user-guide/desktop/configure/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Configure Aembit Desktop settings

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Deliver Aembit Desktop settings to people’s Windows 11 machines as JSON files, so nobody types the Aembit Client ID or the tenant ID by hand. A managed settings file also fixes a value, such as the proxy port, so that nothing on the person’s side overrides it.

## Before you begin

You need:

* Aembit Desktop installed on each machine. [Install Aembit Desktop and sign in](install.md) covers the steps.

* The **Aembit Client ID** from the **Users** tab of your Identity Provider, as described in [Configure Identity Provider user access](../administration/identity-providers/configure-user-access.md).

* Your tenant ID, the first part of your Aembit Tenant UI address: the tenant ID in `https://abc123.aembit.io` is `abc123`.

* A way to place files in `C:\Program Files\Aembit\Desktop\Config\` on each machine, such as your device management tool.

## Choose a settings file

Aembit Desktop reads three settings files. The installer creates none of them, and Aembit Desktop skips any that doesn’t exist.

* **`ManagedSettings.json`**, in `C:\Program Files\Aembit\Desktop\Config\`, holds values people must not change. It overrides every other source.
* **`DefaultSettings.json`**, in the same folder, holds starting values that people can change on their own machine.
* **`Settings.json`**, in `%USERPROFILE%\AppData\Local\AembitDesktop\Config\`, holds a person’s own values, which they edit themselves. You don’t deliver this file.

When the same setting appears in more than one place, the higher source in this list wins:

1. `ManagedSettings.json`.
2. An option on the `adt` command line, such as `adt login --client-id`.
3. An environment variable, such as `AEMBIT_DESKTOP_USER_LOGIN_CLIENT_ID`.
4. The person’s own `Settings.json`.
5. `DefaultSettings.json`.
6. The built-in default.

## Write the settings

Write the file as JSON, with each dotted key name as a nested object. This file sets the Aembit Client ID and the tenant ID:

**ManagedSettings.json**

```json
{
  "tenant": {
    "id": "abc123",
    "login_client_id": "<aembit-client-id>"
  }
}
```

For an Aembit Tenant on the default stack, `useast2.aembit.io`, these two keys let people run `adt login` and `adt get-tenant-ca-cert` without options. For an Aembit Tenant on another stack, add `tenant.stack_domain` to the same file and set it to that stack’s domain.

The keys you can set:

* `tenant.login_client_id` sets the Aembit Client ID that `adt login` uses.
* `tenant.id` sets the tenant ID that `adt get-tenant-ca-cert` uses. Without it, `adt get-tenant-ca-cert` uses the Aembit Tenant of the person’s current sign-in. Signing in doesn’t read it.
* `proxy.http_port` sets the port the local proxy listens on, from 1 to 65535. The default is 8000.
* `tenant.stack_domain` sets the domain of the stack your Aembit Tenant runs in, which `adt login` and `adt get-tenant-ca-cert` both read. The default is `useast2.aembit.io`.
* `log_level` sets how much the background services log: `off`, `trace`, `debug`, `info`, `warn`, or `error`.

The [Aembit Desktop reference](reference.md#settings-keys) lists every key with its default and its environment variable.

Check each key name against that list. Aembit Desktop ignores a key it doesn’t recognize, so a misspelled key has no effect and produces no error. A file that isn’t valid JSON stops `adt start`, `adt login`, and `adt get-tenant-ca-cert` with `Error: Unable to read <path>`, followed by a `Caused by:` line that describes the problem. A value of the right type but outside its allowed range stops `adt start` with a message that states the allowed range, such as `Invalid HTTP proxy port: Must be between 1 and 65535`. An unrecognized `log_level` stops it with `Invalid log level '<value>'`.

## Deliver the file

1. Create the `C:\Program Files\Aembit\Desktop\Config\` folder on each machine if it doesn’t exist.

   The installer doesn’t create it.

2. Copy `ManagedSettings.json`, `DefaultSettings.json`, or both into that folder.

3. On a machine where Aembit Desktop is already running, have the person run `adt stop` and then `adt start`.

   The background services read the settings when they start, so a running service keeps its old values until then. `adt login` and `adt get-tenant-ca-cert` read the settings each time they run.

`adt pristine` removes a person’s own `Settings.json` but keeps the two files in `C:\Program Files\Aembit\Desktop\Config\`.

## Confirm the settings apply

1. On one of the machines, restart the background services:

   ```powershell
   adt stop
   adt start
   ```

   `adt start` prints `HTTP proxy started at:` followed by the address and port the local proxy listens on. The port matches `proxy.http_port` when `ManagedSettings.json` sets it.

2. Sign in without passing a client ID:

   ```powershell
   adt login
   ```

   The browser opens your Identity Provider’s sign-in page. Run this check without `--client-id` and with `AEMBIT_DESKTOP_USER_LOGIN_CLIENT_ID` unset, so that a success means a settings file supplied the client ID. When no source supplies one, `adt login` fails with `You need to specify a login client ID.`
