---
type: how-to
title: "Aembit Edge GitLab CI/CD Component"
description: "How to deploy Aembit Edge Components with GitLab Jobs using the Aembit Edge GitLab CI/CD Component"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/gitlab/gitlab-jobs-component/
interface: web-ui
tags: ["gitlab", "ci-cd", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit Edge GitLab CI/CD Component

This page describes how to use the [Aembit Edge GitLab CI/CD Component](https://gitlab.com/explore/catalog/aembit/aembit-edge) in [GitLab Jobs](https://docs.gitlab.com/ee/ci/jobs/).

> **Enterprise Support**
>
> Aembit supports GitLab Cloud but doesn’t support self-hosted GitLab instances.

The Aembit Edge GitLab CI/CD Component is a pre-built component that you can use in your GitLab pipeline configuration file to retrieve credentials from your Aembit Tenant. It simplifies the process of integrating Aembit Edge with GitLab Jobs by providing a ready-to-use component that handles the authentication and credential retrieval process.

## Configure an Access Policy

To configure your Aembit Tenant to support GitLab Jobs using the Aembit Edge GitLab CI/CD as a Client Workload:

1. Configure your **Client Workload** to identify the Aembit Edge GitLab CI/CD Component runtime environment with one or more of the following Client Workload Identifiers:

   * [GitLab ID Token Namespace Path](../../../access-policies/client-workloads/identification/gitlab-id-token-namespace-path.md)
   * [GitLab ID Token Project Path](../../../access-policies/client-workloads/identification/gitlab-id-token-project-path.md)
   * [GitLab ID Token Ref Path](../../../access-policies/client-workloads/identification/gitlab-id-token-ref-path.md)
   * [GitLab ID Token Subject](../../../access-policies/client-workloads/identification/gitlab-id-token-subject.md)

2. Configure your **Trust Provider** type to [**Gitlab Job ID Token**](../../../access-policies/trust-providers/gitlab-trust-provider.md) to identify and attest the CI/CD component runtime environment.

   Make sure to copy the **Edge SDK Client ID** and any **aud** values for configuration of the GitLab CI/CD component input variables, `client-id` and `aud`.

3. Configure your **Credential Provider** to specify the credential values which you want to be available in the CI runtime environment.

   You can use any [Credential Provider type](../../../access-policies/credential-providers/overview.md). Some may require specifying the `credential_names` GitLab CI/CD component input variable.

4. Configure your **Server Workload** to specify the service endpoint host and port which you want to use in the CI runtime environment.

   You can use any [Server Workload type](../../../access-policies/server-workloads/overview.md). The `server-workload-host` and `server-workload-port` variables must match the values you specify in the Server Workload configuration.

5. Configure your **Access Policy** and then click **Save Policy & Activate**.

## Using the Aembit Edge GitLab CI/CD component

When you have configured your Aembit Tenant to support GitLab Jobs, you can use the Aembit Edge GitLab CI/CD component in your GitLab pipeline configuration file.

You must provide the following required [GitLab CI/CD component input variables](#gitlab-cicd-component-input-variables):

* `client-id` - This is the Edge SDK Client ID from your configured Aembit [GitLab Trust Provider](../../../access-policies/trust-providers/gitlab-trust-provider.md).
* `aud` - This is the **aud** field from your configured Aembit [GitLab Trust Provider](../../../access-policies/trust-providers/gitlab-trust-provider.md).
* `server-workload-host`- This is the server hostname or IP address from your Aembit Server Workload.
* `server-workload-port` - This is the server port number from you Aembit Server Workload.

1. To use the component, specify the `<component-version>` you want to use in the [include section](https://docs.gitlab.com/ci/components/#use-a-component) of your GitLab pipeline configuration file.

   **GitLab pipeline config**

   ```yaml
      ...
      include:
       - component: $CI_SERVER_FQDN/aembit/aembit-edge/aembit-get-credentials@<component-version>
         inputs:
           # `client-id` = Edge SDK Client ID from your Aembit Trust Provider
           client-id: "aembit:useast2:abc123:identity:gitlab_idtoken:0c43ca60-f63f-43be-9801-5a51816fef9b"
           # `aud` = Audience value from your Aembit Trust Provider
           aud: "https://abc123.id.useast2.aembit.io"
           server-workload-host: example.com
           server-workload-port: 443
      ...
   ```

2. Use the credentials (for example, `$TOKEN`, the default credential output name) that your component provides in your GitLab jobs.

   ```yaml
   ...
    my-job:
      script: |
        curl --header "Authorization: Bearer $TOKEN" https://example.com
   ...
   ```

## GitLab CI/CD component input variables

Please review the input variables for the Aembit Edge GitLab CI/CD component in the [GitLab CI/CD catalog entry](https://gitlab.com/explore/catalog/aembit/aembit-edge).

The **Readme** tab provides a full listing, with the input types, descriptions and default values.
