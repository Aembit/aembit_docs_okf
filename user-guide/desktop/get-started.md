---
type: how-to
title: "Get started with Aembit Desktop"
description: "Configure Aembit Desktop from an empty Aembit Tenant setup through a tested GitHub CLI request."
resource: https://docs.aembit.io/user-guide/desktop/get-started/
interface: cli
tags: ["desktop"]
timestamp: 2026-11-04T14:15:50-08:00
---

# Get started with Aembit Desktop

> **Aembit Desktop public beta**
>
> Aembit Desktop is in public beta. Commands and configuration may change before general availability.

Set up Aembit Desktop from an empty Aembit Tenant configuration to a working GitHub CLI request on a Windows 11 machine. You configure an Identity Provider and an Access Policy, install Aembit Desktop, sign in, and confirm that Aembit supplies the GitHub credential.

The example uses the GitHub REST API because it provides a short, observable test: `gh repo list` succeeds with a placeholder token on the workstation while Aembit injects the real token.

## Before you begin

You need:

* Administrator access to your Aembit Tenant and its default Resource Set, where you create every component in this tutorial.

* Administrator access to an Identity Provider that supports OpenID Connect (OIDC) 1.0 or Security Assertion Markup Language (SAML) 2.0.

* A GitHub fine-grained personal access token that can read at least one repository. Follow the [GitHub REST API Key setup](../access-policies/server-workloads/guides/github-rest.md#api-key) if you need to create one.

* A Windows 11 machine where you can approve software installation and certificate trust changes.

* The [GitHub CLI](https://cli.github.com/) installed on that machine. You don’t need to sign in to GitHub CLI.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

Configure an Identity Provider if your Aembit Tenant doesn’t have one

1. Follow [Configure an OIDC 1.0 Identity Provider](../administration/identity-providers/create-idp-oidc.md) or [Configure a SAML 2.0 Identity Provider](../administration/identity-providers/create-idp-saml.md) to register Aembit with your provider and add the provider to your Aembit Tenant.

## Configure Identity Provider user access

1. In the Aembit Tenant UI, click **Administration**, click **Identity Providers**, open the Identity Provider that people use to sign in, and click **Edit**.

2. Click the **Users** tab and turn on **Enable User Access**.

3. Keep **Token Lifetime** at `24 Hours` and **Absolute Token Lifetime** at `30 Day` for this tutorial.

4. Copy the **Aembit Client ID**, then click **Save**.

The person installing Aembit Desktop uses this client ID to sign in. It identifies the Identity Provider for Aembit Desktop sign-in. A Client Workload’s Aembit Client ID is a different value. See [Configure Identity Provider user access](../administration/identity-providers/configure-user-access.md) for the lifetime limits and other available units.

## Create the GitHub Access Policy

The Access Policy connects the signed-in person to the GitHub REST API and its credential. Create all four components in the Access Policy Builder so the example stays together.

### Name the Access Policy

1. Click **Access Policies** in the left sidebar, then click **New**.

2. In the **Access Policy** panel, enter `Aembit Desktop to GitHub` as the **Name**, then click **Save**.

### Configure the Client Workload

The Client Workload identifies requests from people who signed in through the selected Identity Provider.

1. In the **Client Workload** card, click **+ Configure**, then keep the **Add New** tab selected.

2. Enter `Aembit Desktop users` as the **Name**.

3. Under **Client Identification**, select **User Access IDP Client ID** from the **Type** dropdown.

4. Select the Identity Provider where you enabled user access, click the plus button to add the identifier, then click **Save**.

### Configure the Server Workload

The Server Workload represents the GitHub REST API endpoint.

1. In the **Server Workload** card, click **+ Configure**, then keep the **Add New** tab selected.

2. Enter `GitHub REST API` as the **Name**.

3. Under **Service Endpoint**, enter `api.github.com` as the **Host** and select `HTTP` as the **Application Protocol**.

4. Set **Port** to `443` with **TLS** on, and set **Forward to Port** to `443` with **TLS** on.

5. Under **Authentication**, select `HTTP Authentication` as the **Authentication Method** and `Bearer` as the **Authentication Scheme**, then click **Save**.

### Configure the Trust Provider

The Trust Provider limits the Access Policy to the person running the test.

1. In the **Trust Provider** card, click **+ Configure**, then keep the **Add New** tab selected.

2. Enter `Aembit Desktop test user` as the **Name** and select `User Identity Provider` from the **Trust Provider** dropdown.

3. Under **Match Rules**, select `sub` as the **Attribute** and enter the email address used to sign in to the Identity Provider as the **Value**.

   Aembit sets `sub` to the first email address it finds in the NameID, email, or User Principal Name (UPN) claim the Identity Provider sends, in lowercase. When none of them holds an email address, `sub` holds another identifier from the Identity Provider, and a rule that matches an email address doesn’t match.

4. Click **Save**.

### Configure the Credential Provider

The Credential Provider stores the real GitHub token outside the workstation.

1. In the **Credential Provider** card, click **+ Configure**, then keep the **Add New** tab selected.

2. Enter `GitHub fine-grained token` as the **Name** and select `API Key` from the **Credential Type** dropdown.

3. Paste the GitHub fine-grained personal access token into **API Key**, then click **Save**.

4. Click **Save Policy & Activate**.

The Access Policy is ready when each configured component shows a green check in the builder.

## Install Aembit Desktop

Complete the remaining steps on the Windows 11 machine.

1. Download the Windows installer from the [Aembit Desktop releases page](https://docs.aembit.io/releases/desktop/).

2. Run `aembit_desktop_windows_amd64_<version>.msi` and approve the administrator prompt.

3. Open a new PowerShell window and confirm the installation:

   ```powershell
   adt --version
   ```

## Trust the Aembit Tenant certificate authority

Aembit Desktop uses your Aembit Tenant’s certificate authority to handle encrypted requests through the local proxy. Add that certificate authority to the Windows trusted root store before testing GitHub.

1. Replace `abc123` with the tenant ID from the beginning of your Aembit Tenant UI address, then save the certificate:

   ```powershell
   adt get-tenant-ca-cert --tenant-id abc123 --file aembit-tenant-ca.crt
   ```

   `adt` also prints a `certutil` command that trusts the certificate for your Windows user only. The next step trusts it for the whole machine instead.

2. Open PowerShell as an administrator and import the certificate:

   ```powershell
   Import-Certificate -FilePath .\aembit-tenant-ca.crt -CertStoreLocation Cert:\LocalMachine\Root
   ```

3. Close the administrator window, and run the rest of the `adt` commands from a regular PowerShell window. Background services started from an administrator window run with administrator privileges.

## Start Aembit Desktop and sign in

1. Start the background services:

   ```powershell
   adt start
   ```

2. Replace the placeholder with the **Aembit Client ID** you copied from the Identity Provider, then sign in:

   ```powershell
   adt login --client-id <aembit-client-id>
   ```

3. Complete the Identity Provider sign-in in the browser. PowerShell displays `Login successful.` when Aembit Desktop stores the session.

4. Check the services and session:

   ```powershell
   adt status
   ```

## Test GitHub access

GitHub CLI requires a token before it sends a request. Set an invalid placeholder token to let the command start. Aembit replaces it with the Credential Provider’s token as the request passes through the proxy.

1. In the same PowerShell window, open a shell that sends its HTTP and HTTPS traffic to Aembit Desktop:

   ```powershell
   adt beta shell
   ```

   `adt beta shell` opens a new shell with `HTTP_PROXY` and `HTTPS_PROXY` set to the local proxy. The shell is PowerShell unless the `SHELL` environment variable names another program.

2. Set the placeholder token in that shell:

   ```powershell
   $env:GH_TOKEN = "placeholder"
   ```

3. List repositories available to the token stored in the Credential Provider:

   ```powershell
   gh repo list --limit 5
   ```

   A repository list confirms that the request reached GitHub with the Aembit-brokered credential. The placeholder value isn’t a valid GitHub token.

You now have a fully configured and tested Aembit Desktop path from a signed-in person to the GitHub REST API. The real GitHub token stays in the Credential Provider, while the workstation holds the person’s time-limited Aembit session.

## Next steps

* [Set up user access for Aembit Desktop](set-up-user-access.md) - Adapt the Aembit configuration to another HTTP or HTTPS service or add more people
* [Install Aembit Desktop and sign in](install.md) - Review workstation setup, proxy variables, and session cleanup in more detail
* [Use the AWS CLI with Aembit Desktop](aws-cli.md) - Reach AWS through the same signed-in session
* [Troubleshoot Aembit Desktop](troubleshooting.md) - Fix start, sign-in, settings, and certificate errors
* [Aembit Desktop reference](reference.md) - See every `adt` command, option, default, and limit
