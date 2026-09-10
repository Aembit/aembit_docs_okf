---
type: how-to
title: "Deploy Aembit CLI with GitHub Actions"
description: "How to deploy Aembit Edge Components in a Continuous Integration/Continuous Deployment (CI/CD) environment with GitHub Actions using the Aembit Command-Line Interface (CLI)"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/github-edge-cli/
interface: web-ui
tags: ["github", "ci-cd", "deploy-install"]
timestamp: 2026-09-09T21:06:51-07:00
---

# Deploy Aembit CLI with GitHub Actions

You can deploy Aembit edge components using multiple methods. Each method provides similar functionality, but the steps differ.

This page describes how to use the Aembit Command-Line Interface (CLI) in [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions).

> **Simpler alternative available**
>
> Most users should use the **[Aembit GitHub Action](overview.md)** for a streamlined integration. Use this CLI approach when you need advanced CLI options not exposed by the Action.

> **Enterprise Support**
>
> Aembit supports GitHub Cloud but doesn’t support self-hosted GitHub Enterprise Server instances.

## Configure an Access Policy

To configure your Aembit Tenant to support GitHub Actions as a Client Workload:

1. Configure your **Client Workload** using one or more of these Client Identification options.

   * [GitHub Identifier (ID) Token Repository](../../../access-policies/client-workloads/identification/github-id-token-repository.md)
   * [GitHub Identifier (ID) Token Subject](../../../access-policies/client-workloads/identification/github-id-token-subject.md)

   > **Note**
   >
   > As you complete these steps:
   >
   > * For **Step 2** - Copy the provided Client ID and (where appropriate) Audience values for Agent command line parameters.
   > * For **Step 3** - You can use any Credential Provider type. Some types may require the `--credential-names` parameter when running the Aembit CLI.
   > * For **Step 4** - You can use any Server Workload type. The `--server-workload-host` and `--server-workload-port` parameters must match the values you specify.

2. Configure your **Trust Provider** type to [**GitHub Trust Provider**](../../../access-policies/trust-providers/github-trust-provider.md) to identify and attest the Aembit CLI runtime environment.

3. Configure your **Credential Provider** with the credential values for the Continuous Integration (CI) runtime environment.

4. Configure your **Server Workload** with the service endpoint host and port for the CI runtime environment.

5. Configure your **Access Policy** referencing the Aembit entities from steps 3 - 6, and then click **Save Policy & Activate**.

## Configure for use with a custom Resource Set

To configure GitHub Actions to work with a custom [Resource Set](../../../administration/resource-sets/overview.md):

1. Open your existing GitHub Actions configuration file.

2. Go to your Aembit Tenant, click the **Trust Providers** link in the left sidebar and locate your GitLab Trust Provider in the Custom Resource Set you are working with.

3. In your GitHub Actions configuration file, go to the `env` section for the action step and add both the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` values. The following example shows the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` values in the `steps` section:

   **Example GitHub Actions job**

   ```yaml
   jobs:
     sample:
       steps:
         - name: Sample
           env:
             AEMBIT_CLIENT_ID: <_your Client ID_>
             AEMBIT_RESOURCE_SET_ID: <_your Resource Set ID_>
   ```

4. Verify both the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` environment variables match the values in your Resource Set and Trust Provider in your Aembit Tenant.

5. Commit your changes to your GitHub Actions configuration file.

## Deploy the CI/CD script

1. Retrieve the latest Aembit CLI release from the [Aembit CLI releases](https://docs.aembit.io/releases/agent/) page.

2. Include the Aembit CLI within your CI environment. Bundle it within an image, or retrieve it dynamically as appropriate for your workload.

3. Configure your CI job to call the Aembit CLI with the proper parameters. The following example shows a **GitHub Actions** configuration.

   **Example GitHub Actions job**

   ```yaml
       # The id-token permissions value must be set to write for retrieval of the GitHub OpenID Connect (OIDC) Identity Token
       permissions:
         id-token: write
       ...
       jobs:
         sample:
           steps:
             - name: Sample
               env:
                 # Copy the Client ID value from your Trust Provider to this value
                 AEMBIT_CLIENT_ID: <_your Client ID_>
                 # Add AEMBIT_RESOURCE_SET_ID if using a Custom Resource Set
                 # Example: AEMBIT_RESOURCE_SET_ID: 585677c8-9g2a-7zx8-604b-e02e64af11e4
                 # AEMBIT_RESOURCE_SET_ID: <_your Resource Set ID_>
               run: |
                 # Use 'eval' explicitly to ensure the output (for example, 'export TOKEN=...') is executed as shell commands.
                 # The default environment variable name is TOKEN. Override with the --credential-names option.
                 eval $(./aembit credentials get --server-workload-host oauth.sample.com --server-workload-port 443)
                 echo "Open Authorization (OAuth) Token $TOKEN"
   ```

> **Caution**
>
> In the configuration file, replace the value for AEMBIT CLIENT ID with the Client ID value generated on your Trust Provider. Set the Server Workload Host and Server Workload Port values to your desired values.

> **Note**
>
> Set `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` in the environment to run `aembit credentials get` multiple times in the job when all calls rely on the same Trust Provider. To run `aembit credentials get` multiple times in the same job but interact with separate Trust Providers, use the `--client-id` and `--resource-set-id` options instead.

## Verify Aembit CLI

See [Aembit CLI releases](https://docs.aembit.io/releases/agent/) for the current version, download links, and verification steps.
