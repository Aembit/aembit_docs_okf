---
type: how-to
title: "Deploy Aembit CLI with GitLab Jobs"
description: "How to deploy Aembit CLI with GitLab Jobs"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-cli/
interface: web-ui
tags: ["gitlab", "ci-cd", "deploy-install"]
timestamp: 2026-09-09T21:06:51-07:00
---

# Deploy Aembit CLI with GitLab Jobs

This page describes how to use the [Aembit CLI](../../../../dev-guide/cli/overview.md) in [GitLab Jobs](https://docs.gitlab.com/ee/ci/jobs/).

> **Enterprise Support**
>
> Aembit supports GitLab Cloud but doesn’t support self-hosted GitLab instances.

The Aembit CLI provides the `credentials get` command to retrieve credentials from your Aembit Tenant. It simplifies the process of integrating Aembit Edge with GitLab Jobs by providing a command-line interface that handles the authentication and credential retrieval process.

## Configure an Access Policy

To configure your Aembit Tenant to support GitLab Jobs as a Client Workload:

1. Configure your **Client Workload** to identify the Aembit CLI runtime environment with one or more of the following Client Workload Identifiers:

   * [GitLab ID Token Namespace Path](../../../access-policies/client-workloads/identification/gitlab-id-token-namespace-path.md)
   * [GitLab ID Token Project Path](../../../access-policies/client-workloads/identification/gitlab-id-token-project-path.md)
   * [GitLab ID Token Ref Path](../../../access-policies/client-workloads/identification/gitlab-id-token-ref-path.md)
   * [GitLab ID Token Subject](../../../access-policies/client-workloads/identification/gitlab-id-token-subject.md)

2. Configure your **Trust Provider** type to [**GitLab Job ID Token**](../../../access-policies/trust-providers/gitlab-trust-provider.md) to identify and attest the Aembit CLI runtime environment.

   Make sure to copy the provided **Edge SDK Client ID** and any Audience values for configuration of the Aembit CLI parameters.

3. Configure your **Credential Provider** to specify the credential values which you want to be available in the CI runtime environment.

   You can use any [Credential Provider type](../../../access-policies/credential-providers/overview.md). Some may require specifying the [`--credential-names`](../../../../dev-guide/cli/reference/credentials-get.md#--credential-names) parameter when running the Aembit CLI.

4. Configure your **Server Workload** to specify the service endpoint host and port which you want to use in the CI runtime environment.

   You can use any [Server Workload type](../../../access-policies/server-workloads/overview.md). The [`--server-workload-host`](../../../../dev-guide/cli/reference/credentials-get.md#--server-workload-host) and [`--server-workload-port`](../../../../dev-guide/cli/reference/credentials-get.md#--server-workload-port) parameters must match the values you specify in the Server Workload configuration.

5. Configure your **Access Policy** and then click **Save Policy & Activate**.

## Configure a custom Resource Set

To configure a GitLab Job to work with a custom Resource Set:

1. Open your existing GitLab CI configuration file.

2. Go to your Aembit Tenant, click the **Trust Providers** link in the left sidebar and locate your GitLab Trust Provider in the custom Resource Set you are working with.

3. In your `gitlab-ci.yml` file, either:

   * update the `AEMBIT_CLIENT_ID` and add the `AEMBIT_RESOURCE_SET_ID` environment variables if you moving to a custom Resource Set; or
   * add both `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` environment variables if you are just getting started with enabling your workload to use Aembit.

   In the following example, see the `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` environment variables in the `variables` section.

   **gitlab-ci.yml**

   ```yaml
       variables:
           AEMBIT_CLIENT_ID: aembit:stack:tenant:identity:gitlab_idtoken:uuid
           AEMBIT_RESOURCE_SET_ID: bd886157-ba1d-54x86-9f26-3095b0515278
   ```

4. Verify these environment variables match the values in your Resource Set and Trust Provider in your Aembit Tenant.

5. Commit your changes to the GitLab CI configuration file, `.gitlab-ci.yml`.

## Using the Aembit CLI

Please review the [CLI Reference](../../../../dev-guide/cli/reference/credentials-get.md) to review use of the CLI. A GitLab Job specific example follows.

> **Note**
>
> When using the CLI with GitLab the `--id-token` parameter is **required**, its value is the name of a token declared in the [id\_tokens](https://docs.gitlab.com/ci/secrets/id_token_authentication/#id-tokens) section of your CI/CD job.

## Deploy the CI/CD script

1. Retrieve the latest Aembit CLI release from the [Aembit CLI releases](https://docs.aembit.io/releases/agent/) page.

2. Include Aembit CLI within your CI environment. You do this by bundling it within an image or retrieving it dynamically as appropriate for your workload.

3. Configure your CI script to call Aembit CLI with the proper parameters. The following shows an example `gitlab-ci.yml` configuration for a GitLab Job:

   **gitlab-ci.yml**

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
           # Following are samples for OAuth Client Credentials flow, API Key, and Username/Password Credential Provider Types.
           # Please update the --server-workload-host and --server-workload-port values to match your target workloads.
           # Use 'eval' explicitly to run the output, such as 'export TOKEN=abc123', as shell commands.
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host oauth.sample.com --server-workload-port 443)
           - echo "OAuth Token: $TOKEN"
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host apikey.sample.com --server-workload-port 443 --credential-names APIKEY)
           - echo "API Key Example: $APIKEY"
           - eval $(./aembit credentials get --id-token $GITLAB_OIDC_TOKEN --server-workload-host password.sample.com --server-workload-port 443 --credential-names USERNAME,PASSWORD)
           - echo "Username Password Example: $USERNAME -- $PASSWORD"
   ```

> **Caution**
>
> Update the configuration file as follows:
>
> * Replace the `AEMBIT_CLIENT_ID` and `aud` placeholders with the values of Client ID and Audience generated on your Trust Provider.
> * Set the Server Workload Host and Server Workload Port values to your desired values.

> **Note**
>
> Setting `AEMBIT_CLIENT_ID` and `AEMBIT_RESOURCE_SET_ID` in the environment is best suited for when you need to run `aembit credentials get` multiple times in the job that all rely on the same Trust Provider. If you need to run `aembit credentials get` multiple times in the same job but you need to interact with separate Trust Providers, it’s best to use the `--client-id` and `--resource-set-id` options.

## Verify Aembit CLI

See [Aembit CLI releases](https://docs.aembit.io/releases/agent/) for the current version, download links, and verification steps.
