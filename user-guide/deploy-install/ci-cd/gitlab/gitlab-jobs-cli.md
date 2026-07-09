---
type: how-to
title: "Deploy Aembit Edge CLI with GitLab Jobs"
description: "How to deploy Aembit Edge CLI with GitLab Jobs"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-cli/
interface: web-ui
tags: [gitlab, ci-cd, deploy-install]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Deploy Aembit Edge CLI with GitLab Jobs

This page describes how to use the [Aembit Edge CLI](../../../../cli-guide/overview.md) in [GitLab Jobs](https://docs.gitlab.com/ee/ci/jobs/).

Enterprise Support

Aembit supports GitLab Cloud but doesn’t support self-hosted GitLab instances.

The Aembit Edge CLI provides the `credentials get` command to retrieve credentials from your Aembit Tenant. It simplifies the process of integrating Aembit Edge with GitLab Jobs by providing a command-line interface that handles the authentication and credential retrieval process.

## Configure an Access Policy

[Section titled “Configure an Access Policy”](#configure-an-access-policy)

To configure your Aembit Tenant to support GitLab Jobs as a Client Workload:

1. Configure your **Client Workload** to identify the Aembit Edge CLI runtime environment with one or more of the following Client Workload Identifiers:

   * [GitLab ID Token Namespace Path](../../../access-policies/client-workloads/identification/gitlab-id-token-namespace-path.md)
   * [GitLab ID Token Project Path](../../../access-policies/client-workloads/identification/gitlab-id-token-project-path.md)
   * [GitLab ID Token Ref Path](../../../access-policies/client-workloads/identification/gitlab-id-token-ref-path.md)
   * [GitLab ID Token Subject](../../../access-policies/client-workloads/identification/gitlab-id-token-subject.md)

2. Configure your **Trust Provider** type to [**Gitlab Job ID Token**](../../../access-policies/trust-providers/gitlab-trust-provider.md) to identify and attest the Aembit Edge CLI runtime environment.

   Make sure to copy the provided **Edge SDK Client ID** and any Audience values for configuration of the Aembit Edge CLI parameters.

3. Configure your **Credential Provider** to specify the credential values which you want to be available in the CI runtime environment.

   You can use any [Credential Provider type](../../../access-policies/credential-providers/overview.md). Some may require specifying the [`--credential-names`](../../../../cli-guide/reference/credentials-get.md#--credential-names) parameter when running the Aembit Edge CLI.

4. Configure your **Server Workload** to specify the service endpoint host and port which you want to use in the CI runtime environment.

   You can use any [Server Workload type](../../../access-policies/server-workloads/overview.md). The [`--server-workload-host`](../../../../cli-guide/reference/credentials-get.md#--server-workload-host) and [`--server-workload-port`](../../../../cli-guide/reference/credentials-get.md#--server-workload-port) parameters must match the values you specify in the Server Workload configuration.

5. Configure your **Access Policy** and then click **Save Policy & Activate**.

## Configure a custom Resource Set

[Section titled “Configure a custom Resource Set”](#configure-a-custom-resource-set)

To configure a GitLab Job to work with a custom Resource Set:

1. Open your existing GitLab CI configuration file.

2. Go to your Aembit Tenant, click the **Trust Providers** link in the left sidebar and locate your GitLab Trust Provider in the custom Resource Set you are working with.

3. In your `gitlab-ci.yml` file, either:

   * update the `AEMBIT_CLIENT_ID` and add the `AEMBIT_RESOURCE_SET_ID` environment variables if you moving to a custom Resource Set; or
   * add both `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` environment variables if you are just getting started with enabling your workload to use Aembit.

   In the following example, see the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` environment variables in the `variables` section.

   gitlab-ci.yml

   ```yaml
       variables:
           AEMBIT_CLIENT_ID: aembit:stack:tenant:identity:gitlab_idtoken:uuid
           AEMBIT_RESOURCE_SET_ID: bd886157-ba1d-54x86-9f26-3095b0515278
   ```

4. Verify these environment variables match the values in your Resource Set and Trust Provider in your Aembit Tenant.

5. Commit your changes to the GitLab CI configuration file, `.gitlab-ci.yml`.

## Using the Aembit Edge CLI

[Section titled “Using the Aembit Edge CLI”](#using-the-aembit-edge-cli)

Please review the [CLI Reference](../../../../cli-guide/reference/credentials-get.md) to review use of the CLI. A GitLab Job specific example is provided below.

Note

When using the CLI with GitLab the `--id-token` parameter is **required**, its value is the name of a token declared in the [id\_tokens](https://docs.gitlab.com/ci/secrets/id_token_authentication/#id-tokens) section of your CI/CD job.

## Deploy the CI Script

[Section titled “Deploy the CI Script”](#deploy-the-ci-script)

1. Retrieve the latest available [Aembit Edge CLI Releases](https://releases.aembit.io/agent/index.html).

2. Include Aembit Edge CLI within your CI environment. You do this by bundling it within an image or retrieving it dynamically as appropriate for your workload.

3. Configure your CI script to call Aembit Edge CLI with the proper parameters. The following shows an example `gitlab-ci.yml` configuration for a GitLab Job:

   ```yaml
   sample:
       variables:
           # Set this to the value of "Edge SDK Client ID" that is provided in the settings of your Trust Provider.
           AEMBIT_CLIENT_ID: aembit:stack:tenant:identity:gitlab_idtoken:uuid
           # Add AEMBIT_RESOURCE_SET_ID if using a Custom Resource Set
           # Example: AEMBIT_RESOURCE_SET_ID: bd886157-ba1d-54x86-9f26-3095b0515278
           # AEMBIT_RESOURCE_SET_ID: <your_resource_set_id>
       id_tokens:
           GITLAB_OIDC_TOKEN:
               # Set this to the value of "Edge SDK Audience" that is provided in the settings for your Trust Provider.
               aud: https://tenant.id.stack.aembit.io
       script:
           # Following are samples for OAuth Client Credentials flow, API Key, and Username/Password Credential Provider Types
           # Please update the --server-workload-host and --server-workload-port values to match your target workloads
           # Use 'eval' explicitly to ensure the output (for example, 'export TOKEN=...') is executed as shell commands.
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host oauth.sample.com --server-workload-port 443)
           - echo "OAuth Token: $TOKEN"
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host apikey.sample.com --server-workload-port 443 --credential-names APIKEY)
           - echo "API Key Example: $APIKEY"
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host password.sample.com --server-workload-port 443 --credential-names USERNAME,PASSWORD)
           - echo "Username Password Example: $USERNAME -- $PASSWORD"
   ```

Caution

Update the configuration file as follows:

* Replace the `AEMBIT_CLIENT_ID` and `aud` placeholders with the values of Client ID and Audience generated on your Trust Provider.
* Set the Server Workload Host and Server Workload Port values to your desired values.

Note

Setting `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` in the environment is best suited for when you need to run `aembit credentials get` multiple times in the job that all rely on the same Trust Provider. If you need to run `aembit credentials get` multiple times in the same job but you need to interact with separate Trust Providers, it’s best to use the `--client-id` and `--resource-set-id` options.

## Verify Aembit Edge CLI

[Section titled “Verify Aembit Edge CLI”](#verify-aembit-edge-cli)

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
