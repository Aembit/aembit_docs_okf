---
type: how-to
title: "Tutorial: Secure your GitHub Actions workflow with Aembit"
description: "Learn to configure Aembit to deliver credentials to a GitHub Actions workflow"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/github-actions-tutorial/
interface: web-ui
tags: [github, ci-cd, deploy-install]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Tutorial: Secure your GitHub Actions workflow with Aembit

This tutorial shows you how to configure Aembit to deliver an API key to a GitHub Actions workflow. Your workflow retrieves the credential at runtime instead of storing secrets in GitHub.

**Time required:** Approximately 20 minutes

This tutorial uses placeholder values and doesn’t require you to connect to a real external service. You’ll see how Aembit authenticates your workflow, delivers a credential, and logs the access event, demonstrating the complete flow without needing production API credentials.

## How the integration works

[Section titled “How the integration works”](#how-the-integration-works)

The following diagram shows the credential delivery flow when your GitHub Actions workflow runs:

![Credential delivery flow from GitHub Actions through Aembit to a target API](https://docs.aembit.io/d2/docs/user-guide/deploy-install/ci-cd/github/github-actions-tutorial-0.svg)

Your workflow authenticates using GitHub’s built-in OIDC provider, and Aembit validates this identity before delivering the requested credential. The credential exists only during that workflow job.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before starting, ensure you have:

* An [Aembit account](https://useast2.aembit.io/signup) with access to create Aembit Components

* A GitHub repository with:

  * [Actions enabled](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository)
  * `id-token: write` permission
  * An API you want to call from your workflow (this tutorial uses a generic HTTPS API)

## Step 1: Create an Access Policy

[Section titled “Step 1: Create an Access Policy”](#step-1-create-an-access-policy)

The Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) defines who can access what and how credentials are delivered. You’ll create all the required components within the Access Policy Builder.

1. In your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../../get-started/concepts/administration.md), go to **Access Policies** and select **+ New**.

   The Access Policy Builder opens with component cards in the right panel and the **Access Policy** panel open by default.

2. In the **Name** field, enter a name such as `GitHub Actions Demo Policy`.

3. Click **Save Policy** so that you can come back and edit it later if you don’t complete it all in one session.

### Add a Client Workload

[Section titled “Add a Client Workload”](#add-a-client-workload)

The Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) identifies your GitHub repository as an authorized client.

1. In the **Client Workload** card in the right panel, click **+ Configure**.

2. On the **Add New** tab, enter a name such as `github-actions-demo`.

3. From the **Client Identification** dropdown, select **GitHub ID Token Repository**.

4. In the **Value** field, enter your repository in the format `owner/repo` (for example, `my-org/my-repo`).

5. Click **Save**.

### Add a Server Workload

[Section titled “Add a Server Workload”](#add-a-server-workload)

The Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) identifies the API endpoint your workflow accesses.

1. In the **Server Workload** card in the right panel, click **+ Configure**.

2. On the **Add New** tab, enter a name such as `demo-api-server`.

3. In the **Service Endpoint** section:

   * **Host**: Enter `api.example.com` (or your actual API hostname)
   * **Application Protocol**: Select **HTTP**
   * **Port**: Enter `443`
   * **TLS**: Select this checkbox

4. Click **Save**.

### Add a Trust Provider

[Section titled “Add a Trust Provider”](#add-a-trust-provider)

The Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../../get-started/concepts/trust-providers.md) validates GitHub’s OIDC tokens and provides the Client ID for your workflow.

1. In the **Trust Provider** card in the right panel, click **+ Configure**.

2. On the **Add New** tab, enter a name such as `github-actions-trust`.

3. From the **Trust Provider** dropdown, select **GitHub Action ID Token**.

4. In the **Match Rules** section, set the following:

   * **Repository**: Enter your repository in the format `owner/repo` (for example, `my-org/my-repo`)

5. Click **Save**.

6. After saving, copy the **Edge SDK Client ID** value displayed. You need this for your workflow file.

Remember to save your Edge SDK Client ID

Copy the Edge SDK Client ID now. You need it for your GitHub workflow configuration.

### Add a Credential Provider

[Section titled “Add a Credential Provider”](#add-a-credential-provider)

The Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) stores the credential that Aembit delivers to your workflow.

1. In the **Credential Provider** card in the right panel, click **+ Configure**.

2. On the **Add New** tab, enter a name such as `demo-api-credential`.

3. From the **Credential Type** dropdown, select **API Key**.

4. In the **API Key** field, enter your API key value (this demo uses `Aembit-Docs-Demo-Test-K3y!`).

5. Click **Save**.

### Save and activate the policy

[Section titled “Save and activate the policy”](#save-and-activate-the-policy)

1. In the **Access Policy** status card on the left, verify each required component shows a green **Configured** status.

2. Click **Save Policy & Activate** in the header bar.

## Step 2: Configure your GitHub workflow

[Section titled “Step 2: Configure your GitHub workflow”](#step-2-configure-your-github-workflow)

Create a workflow file that uses the Aembit GitHub Action to retrieve credentials.

Required permissions

GitHub requires the `id-token: write` permission to issue the OIDC token that Aembit validates. Without this permission, the action fails.

1. In your GitHub repository, create a new file `.github/workflows/aembit-demo.yml`.

2. Add the following content:

   .github/workflows/aembit-demo.yml

   ```yaml
   name: Aembit Demo


   on:
     workflow_dispatch:


   permissions:
     id-token: write
     contents: read


   jobs:
     call-api:
       runs-on: ubuntu-latest
       steps:
         - name: Get credentials from Aembit
           id: aembit
           uses: Aembit/get-credentials@v1
           with:
             client-id: '<YOUR_EDGE_SDK_CLIENT_ID>'
             credential-type: 'ApiKey'
             server-host: '<YOUR_API_HOSTNAME>'
             server-port: '443'


         # For demo purposes only - displays the credential in logs
         - name: Verify credential was retrieved
           env:
             API_KEY: ${{ steps.aembit.outputs.api-key }}
           run: |
             echo "API_KEY is set: $([ -n "$API_KEY" ] && echo 'yes' || echo 'no')"
             echo "API_KEY length: ${#API_KEY} characters"
             echo "API Key value (characters separated to bypass GitHub masking):"
             echo -n "$API_KEY" | sed 's/./& /g'
   ```

   Demo only

   The verification step above displays the credential in workflow logs. **Never use this in production workflows.** Remove this step before using the workflow with real credentials.

   Output names vary by credential type

   This example uses `api-key` because the credential type is `ApiKey`. Other credential types use different output names (for example, `token` for OAuth). See the [Action output reference](github-actions-reference.md) for the complete list.

3. Replace the highlighted placeholder values:

   * `<YOUR_EDGE_SDK_CLIENT_ID>`: Enter the **Edge SDK Client ID** you copied from your Trust Provider.
   * `<YOUR_API_HOSTNAME>`: Enter the same hostname from your Server Workload (for example, `api.example.com`).

4. Commit and push the workflow file.

## Step 3: Run and verify

[Section titled “Step 3: Run and verify”](#step-3-run-and-verify)

1. Go to your repository’s **Actions** tab in GitHub.

2. Select the **Aembit Demo** workflow from the left sidebar.

3. Select **Run workflow** and confirm.

4. Watch the workflow run. You should see:

   * The Aembit action retrieving credentials
   * Your API call completing successfully

5. In your Aembit Tenant, go to **Reporting** > **Access Authorization Events**.

6. Verify you see an event for your workflow’s credential request with status **Authorized**.

### Expected output

[Section titled “Expected output”](#expected-output)

A successful workflow run shows output similar to:

GitHub Actions log

```text
Run Aembit/get-credentials@v1
Client ID is valid ✅
ApiKey is a valid credential type ✅
Fetching token ID for https://xxxxxx.id.aembit.io
Fetch access token (url): https://xxxxxx.ec.aembit.io/edge/v1/auth
Response status: 200
Fetch Credential (url): https://xxxxxx.ec.aembit.io/edge/v1/credentials
Response status: 200
```

Verification step output

```text
Credential verification:
  API_KEY is set: yes
  API_KEY length: 26 characters


API Key value (characters separated to bypass masking):
A e m b i t - D o c s - D e m o - T e s t - K 3 y !
```

The highlighted lines confirm:

* The action validated your Client ID and credential type
* Both API calls to Aembit returned `200` (success)
* The credential was retrieved and is available in your workflow

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

### JSON parsing error

[Section titled “JSON parsing error”](#json-parsing-error)

**Full error:**

```text
Fetch access token (url): https://xxxxxx.ec.useast2.aembit.io/edge/v1/auth
Error: Unexpected token '<', "<html>
<h"... is not valid JSON
```

**Cause:** The action is connecting to the wrong Aembit environment. This typically happens when your Aembit Tenant is in a different environment (such as QA or EU) than the default production environment.

**Solution:** Add the `domain` parameter to specify your Aembit environment:

```yaml
with:
  client-id: '<YOUR_EDGE_SDK_CLIENT_ID>'
  credential-type: 'ApiKey'
  server-host: '<YOUR_API_HOSTNAME>'
  server-port: '443'
  domain: '<YOUR_AEMBIT_DOMAIN>'
```

Your domain is visible in your Aembit Tenant URL. For example, if your tenant URL is `https://mytenant.qa.aembit.io`, your domain is `qa.aembit.io`.

### Error: Authorization failed

[Section titled “Error: Authorization failed”](#error-authorization-failed)

**Cause:** The Access Policy configuration doesn’t match your workflow.

**Solution:** Verify these components match:

* **Client Workload:** The repository value matches your GitHub repository exactly (`owner/repo`)
* **Trust Provider:** Has a match rule for your repository
* **Server Workload:** The host and port match the values in your workflow
* **Access Policy:** Is active and links all components

## Congratulations!

[Section titled “Congratulations!”](#congratulations)

Your GitHub Actions workflow now retrieves credentials from Aembit at runtime. GitHub stores no secrets, and Aembit logs every credential request for auditing.

## What’s next?

[Section titled “What’s next?”](#whats-next)

Now that you’ve completed the basic setup:

* **[Use other credential types](github-actions-how-to.md)** - Configure Open Authorization (OAuth) tokens, username/password, and more
* **[Review the action reference](github-actions-reference.md)** - See all available action parameters
* **[View audit logs](../../../audit-report/access-authorization-events.md)** - Monitor credential usage across workflows
* **[Add access conditions](../../../access-policies/access-conditions/overview.md)** - Restrict access based on time, location, or device posture
