---
type: how-to
title: "Deploy Aembit Edge CLI with GitHub Actions"
description: "How to deploy Aembit Edge Components in a Continuous Integration/Continuous Deployment (CI/CD) environment with GitHub Actions using the Aembit Command-Line Interface (CLI)"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/github-edge-cli/
interface: web-ui
tags: [github, ci-cd, deploy-install]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Deploy Aembit Edge CLI with GitHub Actions

You can deploy Aembit edge components using multiple methods. Each method provides similar functionality, but the steps differ.

This page describes how to use the Aembit Edge Command-Line Interface (CLI) in [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions).

Simpler alternative available

Most users should use the **[Aembit GitHub Action](overview.md)** for a streamlined integration. Use this CLI approach when you need advanced CLI options not exposed by the Action.

Enterprise Support

Aembit supports GitHub Cloud but doesn’t support self-hosted GitHub Enterprise Server instances.

## Configure an Access Policy

[Section titled “Configure an Access Policy”](#configure-an-access-policy)

To configure your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../../get-started/concepts/administration.md) to support GitHub Actions as a Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md):

1. Configure your **Client Workload** using one or more of these Client Identification options.

   * [GitHub Identifier (ID) Token Repository](../../../access-policies/client-workloads/identification/github-id-token-repository.md)
   * [GitHub Identifier (ID) Token Subject](../../../access-policies/client-workloads/identification/github-id-token-subject.md)

   Note

   As you complete these steps:

   * For **Step 2** - Copy the provided Client ID and (where appropriate) Audience values for Agent command line parameters.
   * For **Step 3** - You can use any Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) type. Some types may require the `--credential-names` parameter when running the Aembit Agent.
   * For **Step 4** - You can use any Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) type. The `--server-workload-host` and `--server-workload-port` parameters must match the values you specify.

2. Configure your **Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../../get-started/concepts/trust-providers.md)** type to [**GitHub Trust Provider**](../../../access-policies/trust-providers/github-trust-provider.md) to identify and attest the Aembit Agent runtime environment.

3. Configure your **Credential Provider** with the credential values for the Continuous Integration (CI) runtime environment.

4. Configure your **Server Workload** with the service endpoint host and port for the CI runtime environment.

5. Configure your **Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md)** referencing the Aembit entities from steps 3 - 6, and then click **Save Policy & Activate**.

## Configure for use with a custom Resource Set

[Section titled “Configure for use with a custom Resource Set”](#configure-for-use-with-a-custom-resource-set)

To configure GitHub Actions to work with a custom [Resource Set](../../../administration/resource-sets/overview.md):

1. Open your existing GitHub Actions configuration file.

2. Go to your Aembit Tenant, click the **Trust Providers** link in the left sidebar and locate your GitLab Trust Provider in the Custom Resource Set you are working with.

3. In your GitHub Actions configuration file, go to the `env` section for the action step and add both the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` values. The following example shows the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` values in the `steps` section:

   Example GitHub Actions job

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

## Deploy the CI script

[Section titled “Deploy the CI script”](#deploy-the-ci-script)

1. Retrieve the latest Aembit Agent release from the [Agent Releases](https://releases.aembit.io/agent/index.html) page.

2. Include the Aembit Agent within your CI environment. Bundle it within an image, or retrieve it dynamically as appropriate for your workload.

3. Configure your CI job to call the Aembit Agent with the proper parameters. The following example shows a **GitHub Actions** configuration.

   Example GitHub Actions job

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

Caution

In the configuration file, replace the value for AEMBIT CLIENT ID with the Client ID value generated on your Trust Provider. Set the Server Workload Host and Server Workload Port values to your desired values.

Note

Set `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` in the environment to run `aembit credentials get` multiple times in the job when all calls rely on the same Trust Provider. To run `aembit credentials get` multiple times in the same job but interact with separate Trust Providers, use the `--client-id` and `--resource-set-id` options instead.

## Verify Aembit Agent

[Section titled “Verify Aembit Agent”](#verify-aembit-agent)

To verify the Aembit Agent release, follow these steps using the `gpg` and `shasum` commands. Select the tab that matches your operating system and architecture:

* Linux - amd64

  1. Download the Aembit Agent release version from the [Aembit Agent Releases page](https://releases.aembit.io/agent/index.html) along with the matching checksum files.

     Alternatively, you can download these files using `curl`, swapping out the highlighted release version with the version you're verifying:

     ```shell
     curl -O https://releases.aembit.io/agent/1.31.4764/linux/amd64/aembit_agent_cli_linux_amd64_1.31.4764.tar.gz
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256.sig
     ```

  2. Import Aembit's public GPG key from [Keybase](https://keybase.io/aembit) into `gpg`:

     ```shell
     curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
     ```

  3. Verify Aembit Agent's checksum integrity and authenticity with `gpg`:

     ```shell
     gpg --verify aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256.sig aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256
     ```

     *If you don't have `gpg` installed, see [Verifying Aembit binary release signatures prerequisites](../../verify-releases.md#prerequisites)*.

     Your output should look similar to the following and include the highlighted line:

     ```shell
     gpg --verify aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256.sig aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256
     gpg: Signature made Wed Sep 18 10:13:57 2024 PDT
     gpg:                using RSA key EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
     gpg: Good signature from "Aembit, Inc. <keybase@aembit.io>" [unknown]
     ...
     ```

     As long as you see `Good signature...`, you know that the checksum files are valid and authentic.

     Warnings explained

     * **"\[unknown]"** means you haven't explicitly told GPG to trust this particular signing key.
     * **"WARNING: This key is not certified with a trusted signature!"** is GPG being cautious. GPG can verify the signature is cryptographically valid, but it doesn't know if you trust that this key actually belongs to Aembit.

  4. Verify the integrity of the Aembit Agent file you downloaded using `shasum`:

     ```shell
     shasum -a 256 aembit_agent_cli_linux_amd64_1.31.4764.tar.gz.sha256
     ```

     If `shasum` returns a match, you know the file is intact and matches Aembit's original. The long hex string is the SHA256 hash that both your file and the checksums file agree on. No output would mean the checksums don't match.

* Linux - arm64

  1. Download the Aembit Agent release version from the [Aembit Agent Releases page](https://releases.aembit.io/agent/index.html) along with the matching checksum files.

     Alternatively, you can download these files using `curl`, swapping out the highlighted release version with the version you're verifying:

     ```shell
     curl -O https://releases.aembit.io/agent/1.31.4764/linux/arm64/aembit_agent_cli_linux_arm64_1.31.4764.tar.gz
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256.sig
     ```

  2. Import Aembit's public GPG key from [Keybase](https://keybase.io/aembit) into `gpg`:

     ```shell
     curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
     ```

  3. Verify Aembit Agent's checksum integrity and authenticity with `gpg`:

     ```shell
     gpg --verify aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256.sig aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256
     ```

     *If you don't have `gpg` installed, see [Verifying Aembit binary release signatures prerequisites](../../verify-releases.md#prerequisites)*.

     Your output should look similar to the following and include the highlighted line:

     ```shell
     gpg --verify aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256.sig aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256
     gpg: Signature made Wed Sep 18 10:13:57 2024 PDT
     gpg:                using RSA key EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
     gpg: Good signature from "Aembit, Inc. <keybase@aembit.io>" [unknown]
     ...
     ```

     As long as you see `Good signature...`, you know that the checksum files are valid and authentic.

     Warnings explained

     * **"\[unknown]"** means you haven't explicitly told GPG to trust this particular signing key.
     * **"WARNING: This key is not certified with a trusted signature!"** is GPG being cautious. GPG can verify the signature is cryptographically valid, but it doesn't know if you trust that this key actually belongs to Aembit.

  4. Verify the integrity of the Aembit Agent file you downloaded using `shasum`:

     ```shell
     shasum -a 256 aembit_agent_cli_linux_arm64_1.31.4764.tar.gz.sha256
     ```

     If `shasum` returns a match, you know the file is intact and matches Aembit's original. The long hex string is the SHA256 hash that both your file and the checksums file agree on. No output would mean the checksums don't match.

* Windows - amd64

  1. Download the Aembit Agent release version from the [Aembit Agent Releases page](https://releases.aembit.io/agent/index.html) along with the matching checksum files.

     Alternatively, you can download these files using `curl`, swapping out the highlighted release version with the version you're verifying:

     ```shell
     curl -O https://releases.aembit.io/agent/1.31.4764/windows/amd64/aembit_agent_cli_windows_amd64_1.31.4764.zip
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256
     curl -O https://releases.aembit.io/agent/1.31.4764/aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256.sig
     ```

  2. Import Aembit's public GPG key from [Keybase](https://keybase.io/aembit) into `gpg`:

     ```shell
     curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
     ```

  3. Verify Aembit Agent's checksum integrity and authenticity with `gpg`:

     ```shell
     gpg --verify aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256.sig aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256
     ```

     *If you don't have `gpg` installed, see [Verifying Aembit binary release signatures prerequisites](../../verify-releases.md#prerequisites)*.

     Your output should look similar to the following and include the highlighted line:

     ```shell
     gpg --verify aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256.sig aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256
     gpg: Signature made Wed Sep 18 10:13:57 2024 PDT
     gpg:                using RSA key EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
     gpg: Good signature from "Aembit, Inc. <keybase@aembit.io>" [unknown]
     ...
     ```

     As long as you see `Good signature...`, you know that the checksum files are valid and authentic.

     Warnings explained

     * **"\[unknown]"** means you haven't explicitly told GPG to trust this particular signing key.
     * **"WARNING: This key is not certified with a trusted signature!"** is GPG being cautious. GPG can verify the signature is cryptographically valid, but it doesn't know if you trust that this key actually belongs to Aembit.

  4. Verify the integrity of the Aembit Agent file you downloaded using `shasum`:

     ```shell
     shasum -a 256 aembit_agent_cli_windows_amd64_1.31.4764.zip.sha256
     ```

     If `shasum` returns a match, you know the file is intact and matches Aembit's original. The long hex string is the SHA256 hash that both your file and the checksums file agree on. No output would mean the checksums don't match.
