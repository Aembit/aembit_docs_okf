---
type: how-to
title: "Get started with Aembit Edge API"
description: "Learn how to integrate Aembit Edge API into your cloud-native applications for secure credential retrieval"
resource: https://docs.aembit.io/dev-guide/api/edge/quickstart-edge/
interface: api
tags: ["edge", "api"]
timestamp: 2026-09-22T11:51:31-07:00
---

# Get started with Aembit Edge API

In this quickstart, you’ll learn how to set up Aembit Edge API with either GitHub or GitLab CI/CD to securely retrieve credentials for your cloud-native application.

This guide walks you through the following primary steps:

1. [Connect Aembit to GitHub or GitLab](#step-1-connect-aembit-to-github-or-gitlab)
2. [Configure your Aembit Tenant](#step-2-complete-aembit-config)
3. [Make your first API calls to retrieve credentials](#step-3-make-your-first-api-calls)

## Before you begin

This quickstart assumes you have a basic understanding of [how Aembit works](../../../get-started/how-aembit-works.md) and GitHub or GitLab basic concepts.

> **Enterprise support**
>
> Aembit supports GitHub Cloud and GitLab Cloud but doesn’t support self-hosted GitHub Enterprise Server or GitLab instances.

To follow this quickstart, you must have the following:

* The `tenantId` from your Aembit Tenant (`https://<tenantId>.aembit.io`).\
  *[Create a free Aembit Tenant](https://useast2.aembit.io/signup) if you don’t have one.*

* Either a:

  * GitHub account with:

    * Access to create repositories
    * A email verified GitHub account

  * GitLab account with:

    * Access to create projects/repositories
    * A email verified GitLab account
    * SSH keys configured for GitLab (see [Use SSH keys to communicate with GitLab](https://docs.gitlab.com/user/ssh/) for more info)

* (Optional) Postman installed to use the [Aembit Edge API Postman Collection](https://docs.aembit.io/aembit-edge-api.postman_collection.json)

## Step 1: Connect Aembit to GitHub or GitLab

For the initial setup of Aembit Edge API, you’ll:

1. create a GitHub or GitLab repository
2. create a Trust Provider in your Aembit Tenant to authenticate your CI/CD job’s identity
3. configure a CI configuration file that generates a JSON Web Token (JWT) in your CI/CD pipeline

### Create a git repository

Select the appropriate tab and follow the steps within it to create a new repository in GitHub or GitLab where your CI/CD configuration file resides.

* GitHub

  1. Go to <https://github.com/>, login, and create a **New repository**.

  2. Select **Create a new repository**, and give it a descriptive **Repository name**. For this quickstart, name it `edge-api-test`.

  3. For **Visibility**, choose **Private**.

  4. Click **Create repository**.

     Notice the URL of your new repository, which should look like this:

     ```shell
     https://github.com/<username>/edge-api-test
     ```

     Where `<username>` is your GitHub username and `edge-api-test` is your repository name.

     Keep note of the repository name (`<username>/edge-api-test`). This repository name is important because it will be used to match your GitHub Action with the Aembit Trust Provider you create later.

* GitLab

  1. Go to <https://gitlab.com/>, login, and create a **New project/repository**.

  2. Select **Create blank project**, and give it a descriptive **Project name**. For this quickstart, name it `edge-api-test`.

  3. For **Visibility Level**, choose **Private**.

  4. Click **Create project**.

     Notice the URL of your new project, which should look like this:

     ```shell
     https://gitlab.com/<group>/edge-api-test
     ```

     Where `<group>` is your GitLab username or group name and `edge-api-test` is your project name.

     Keep note of the project path (`<group>/edge-api-test`). This project path is important because it will be used to match your GitLab CI job with the Aembit Trust Provider you create later.

### Create an Aembit Trust Provider

For Aembit to verify the identity of your CI/CD job, you need to create a Trust Provider that uses the either a GitHub Action or GitLab Job ID Token. The Trust Provider you create automatically generates two important values:

* An **Edge SDK Client ID** that you’ll use in your API calls.
* An **Edge SDK Audience** that you’ll use in the CI/CD configuration file.

Select the appropriate tab to follow the steps for creating a Trust Provider for either GitHub or GitLab:

* GitHub

  1. **Open a new browser tab or window**.\
     This is helpful as you’ll be switching between your GitHub repository and your Aembit Tenant.

  2. **Log into your Aembit Tenant** at `https://<tenantId>.aembit.io`.

     Go to **Trust Providers** in the left sidebar menu.

     Click **+ New**.

     ![Aembit Tenant UI Trust Provider page](https://docs.aembit.io/_astro/edge-api-quickstart-trust-provider.7U0kV7Rl_bgKns.webp)

  3. Fill out the **Trust Provider** form:

     * **Name** - Enter a descriptive name like `Edge API GitHub Test`.
     * **TRUST PROVIDER** - Select **GitHub Action ID Token**, which reveals its configuration options.
     * **Edge SDK Audience** - You’ll use this auto-generated value in your CI/CD config file.
     * **Match Rules** - Select **repository** and enter the repository name as the **Value**.\
       Use the repository name you created in the previous step (like `<username>/edge-api-test`).

     ![Aembit Trust Provider form completed](https://docs.aembit.io/_astro/edge-api-quickstart-trust-provider-form-github.mm27kkWI_yYONn.webp)

  4. Click **Save**.

     Aembit displays your new Trust Provider in the list of Trust Providers.

  5. Click on your newly created Trust Provider to view its details.

     You’ll see **Edge SDK Client ID** and **Edge SDK Audience** that Aembit automatically generated for your Trust Provider.

     ![Aembit Trust Provider form completed](https://docs.aembit.io/_astro/edge-api-quickstart-trust-provider-form-github-complete.DPn3VyjL_1VQIER.webp)

     Keep this tab or window open, as you’ll need these values in the next steps.

* GitLab

  1. **Open a new browser tab or window**.\
     This is helpful as you’ll be switching between your GitLab project and your Aembit Tenant.

  2. **Log into your Aembit Tenant** at `https://<tenantId>.aembit.io`.

     Go to **Trust Providers** in the left sidebar menu.

     Click **+ New**.

  3. Fill out the **Trust Provider** form:

     * **Name** - Enter a descriptive name like `Edge API GitLab Test`.
     * **TRUST PROVIDER** - Select **GitLab Job ID Token**, which reveals its configuration options.
     * **OIDC Endpoint** - Enter `https://gitlab.com`.
     * **Edge SDK Audience** - You’ll use this auto-generated value in your CI/CD config file.
     * **Match Rules** - Select **project\_path** and enter project path as the **Value**.\
       Use the project path you created in the previous step (like `<group>/edge-api-test`).

     ![Aembit Trust Provider form](https://docs.aembit.io/_astro/edge-api-quickstart-trust-provider-form-gitlab.DWlXjJeu_Z1UBch5.webp)

  4. Click **Save**.

     Aembit displays your new Trust Provider in the list of Trust Providers.

  5. Click on your newly created Trust Provider to view its details.

     You’ll see **Edge SDK Client ID** and **Edge SDK Audience** that Aembit automatically generated for your Trust Provider.

     ![Aembit Trust Provider form completed](https://docs.aembit.io/_astro/edge-api-quickstart-trust-provider-form-gitlab-complete.NUmvW4BZ_2nyVXx.webp)

     Keep this tab or window open, as you’ll need these values in the next steps.

### Create a CI/CD configuration file

Now you’ll create the CI/CD configuration file that runs in your GitHub or GitLab repository. This file uses the Trust Provider you created to authenticate your CI/CD job with Aembit Edge API. The CI/CD job generates an OpenID Connect (OIDC) token that Aembit Edge API uses to verify the identity of your CI/CD job.

The CI configuration file also prints the Base64-encoded OIDC token, which you’ll decode later to retrieve your identity token. The OIDC token is a JWT that contains information about the identity of your CI/CD job.

The CI configuration file is different for GitHub and GitLab, so be sure to follow the steps for your chosen provider:

* GitHub

  1. Go back to your GitHub repository in the browser tab or window you opened earlier.

     In your new repository, click **Add file** and select **Create new file**.

  2. Create a new file called `.github/workflows/aembit-edge-api.yml`.\
     Paste the entire filepath into the **Name your file…** field and GitHub automatically creates the directories.

     Add the following content to the file:

     **.github/workflows/aembit-edge-api.yml**

     ```yaml
     # Generate an OIDC token for Aembit Edge API with GitHub
     name: Generate OIDC Token


     on:
       workflow_dispatch:
       push:
         branches: [main]


     jobs:
       demo:
         runs-on: ubuntu-latest
         permissions:
           id-token: write
           contents: read


         steps:
           - name: Get OIDC Token
             uses: actions/github-script@v7
             with:
               script: |
                 const token = await core.getIDToken('<trustProviderAudience>');
                 const encoded = Buffer.from(token).toString('base64');
                 console.log('Token (split to avoid masking):');
                 console.log(encoded.substring(0, 50));
                 console.log(encoded.substring(50, 100));
                 console.log(encoded.substring(100));
     ```

  3. Replace `<trustProviderAudience>` with the **Edge SDK Audience** value you copied from your Trust Provider configuration in the previous step. This value is crucial as it tells GitHub to generate an OIDC token with the correct audience for Aembit Edge API.

     The value should look something like this:

     ```shell
     https://<tenantId>.id.useast2.aembit.io
     ```

     This value is crucial as it tells GitHub to generate an OIDC token with the correct audience for Aembit Edge API.

* GitLab

  1. Go back to your GitLab project in the browser tab or window you opened earlier.

     In your new project, click **Edit** and select **Web IDE**, which opens the web-based integrated development environment.

  2. Create a new file called `.gitlab-ci.yml` in the root of your project with the following content:

     **.gitlab-ci.yml**

     ```yaml
     # Generate an OIDC token for Aembit Edge API with GitLab
     demo:
       image: alpine
       id_tokens:
         GITLAB_OIDC_TOKEN:
           aud: <Aembit_Edge_SDK_Audience>
       script:
         - echo "Token:"
         - echo -n $GITLAB_OIDC_TOKEN | base64
     ```

  3. Replace `<Aembit_Edge_SDK_Audience>` with the **Edge SDK Audience** value you copied from your Trust Provider configuration in the previous step. This value is crucial as it tells GitLab to generate an OIDC token with the correct audience for Aembit Edge API.

     The value should look something like this:

     ```shell
     https://<tenantId>.id.useast2.aembit.io
     ```

     This value is crucial as it tells GitLab to generate an OIDC token with the correct audience for Aembit Edge API.

  4. Click **Source Control** in the left side menu.

     Click **Commit and push to ‘main’** to save the file to your repository.

### Generate an OIDC token

Now that you have your CI configuration file set up, it’s time to run it and generate the OIDC token.

* GitHub

  1. While still in your GitHub repository, click on the **Actions** tab at the top of the page.

  2. In the left sidebar menu, you should see your workflow named **Generate OIDC Token**.

     Click on it to view the workflow runs.

  3. Click on the latest run to view its details.

     Click on the “Demo” run.

     Then, expand the **Get OIDC Token** section to reveal the full logs.

  4. You should see the output containing the `Token:` value which is standard Base64-encoded and should look something like this (*the output is split so that GitHub won’t mask the token in the log output*.):

     **GitHub Action Output**

     ```shell
     ...
     Token (split to avoid masking):
     ZXlKaGJHY2lPaUpTVXpJMU5pSXNJbXRwWkNJNklqTTRPREkyWW
     pFM0xUWmhNekF0TldZNVlpMWlNVFk1TFRoaVpXSTRNakF5Wmpj
     eU15SXNJblI1Y0NJNklrcFhWQ0lzSW5nMWRDSTZJbmxyVG1GWk5IRk5YM1JoTkdzeVZHZGFUME5G
     ... omitted for brevity ...
     ZHZjbXRtYkc5M1gyUnBjM0JoZEdOb0lpd2laWGh3SWpveE56VXdNVE0xTlRZeUxDSm9aV0ZrWDNK
     bFppSTZJaUlzSW1saGRDSTZNVGMxTURFeE16azJNaXdpYVhOeklqb2
     ```

  5. Copy the Base64-encoded token from the job output.

  6. Decode the token using a Base64 decoder like [BASE64 Decode and Encode](https://www.base64decode.org/).\
     The decoded token should start with `ey` and should look something like this:

     **Decoded GitHub OIDC Token**

     ```shell
     eyJraWQiOiI0aTNzRkU3c3hxTlBPVDdGZHZjR0ExWlZHR0lfci10c0RYbkV1WVQ0WnFFI
     iwidHlwIjoianNvbiIsImFsZyI6IlJTMjU2In0.e3JuYW1lc3BhY2VfaWQiOiIxMDgzOT
     ... omitted for brevity ...
     1TCmnNp0ubYcDFn5lfpiJyhTCMzKpABlyaqq03CZFSJGkgVMVfHUWKkbBvl-gt1IJFnUq
     gdwZ3QxSUpGblVxUEhzRkFnNm90ZVVrZTlLa2JaRkM0clc1RUZTUEFqSmFWV2lhUlj59D
     ```

     This is your GitHub identity token! Hold onto it, as you’ll need it to authenticate with Aembit.

  Now that you have your GitHub identity token, you must finish configuring Aembit to complete this quickstart guide.

* GitLab

  1. While still in your GitLab project, click on the **CI/CD** tab in the left sidebar menu.

     Click on **Pipelines** to view the list of pipelines.

  2. You should see your pipeline named **Generate OIDC Token**.

     Click on it to view the pipeline runs.

  3. Click on the latest run to view its details.

  4. In the **Jobs** section, click on the job that corresponds to your pipeline (something like `#10346670123: demo`).

  5. The output contains the `Token:` value which is standard Base64-encoded with padding and should look something like this:

     **GitLab Job Output**

     ```shell
     ...
     Token:
     ZXlKcmFXUWlPaUkwYVROelJrVTNjM2h4VGxCUFZEZEdaSFpqUjBFeFdsWkhSMGxmY2kxMGMwUlli
     a1YxV1ZRMFduRkZJaXdpZEhsd0lqb2lTbGRVSWl3aVlXeG5Jam9pVWxNeU5UWWlmUS5leUp1WVcx
     ... omitted for brevity ...
     dmwtZ3QxSUpGblVxUEhzRkFnNm90ZVVrZTlLa2JaRkM0clc1RUZTUEFqSmFWV2lhUkg0cUF6ckZP
     anJ3R3Bva1dQTW9pTlczVU0yNjBRZkVCVWluZTZFeHc=
     ```

  6. Copy the Base64-encoded token from the job output.

  7. Decode the token using a Base64 decoder like [BASE64 Decode and Encode](https://www.base64decode.org/).\
     The decoded token should start with `ey` and should look something like this:

     **Decoded GitLab OIDC Token**

     ```shell
     eyJraWQiOiI0aTNzRkU3c3hxTlBPVDdGZHZjR0ExWlZHR0lfci10c0RYbkV1WVQ0WnFFI
     iwidHlwIjoianNvbiIsImFsZyI6IlJTMjU2In0.e3JuYW1lc3BhY2VfaWQiOiIxMDgzOT
     ... omitted for brevity ...
     1TCmnNp0ubYcDFn5lfpiJyhTCMzKpABlyaqq03CZFSJGkgVMVfHUWKkbBvl-gt1IJFnUq
     gdwZ3QxSUpGblVxUEhzRkFnNm90ZVVrZTlLa2JaRkM0clc1RUZTUEFqSmFWV2lhUlj59D
     ```

     This is your GitLab identity token! Hold onto it, as you’ll need it to authenticate with Aembit.

  Now that you have your GitLab identity token, you must finish configuring Aembit to complete this quickstart guide.

> **Token expiration**
>
> The OIDC token you generate in your CI/CD job is only valid for 1 hour. If you complete all the steps correctly but still get 401 Unauthorized errors when making API calls, run your CI/CD job again to generate a fresh token.

## Step 2: Complete Aembit config

Now that you have your identity token from your CI/CD job, you’re ready to finish configuring the remaining Aembit components to complete this quickstart guide.

In this section, you’ll create:

* a Client Workload that represents your CI/CD job
* a Credential Provider that defines the credentials for your target service
* a Server Workload that represents the target service you want to retrieve credentials for
* an Access Policy that ties everything together

### Create a Client Workload

Create a Client Workload that represents your CI/CD job. This Client Workload is what authenticates with Aembit using the identity token you just generated. You must create this Client Workload so you can retrieve credentials for your target service later using the Aembit Edge API.

* GitHub

  This Client Workload represents your GitHub CI job that authenticates with Aembit using the identity token.

  1. Switch back to the tab or window with your Aembit Tenant, go to **Client Workloads** in the left sidebar menu.

     Click **+ New**.

  2. Fill out the **Client Workload** form:

     * **Name** - Enter a descriptive name like `GitHub Edge API Test`.
     * **Client Identification** - Select **GitHub ID Token Repository** and enter the repository name (`<username>/edge-api-test`) you created earlier as the **Value**.

     ![Aembit Client Workload form](https://docs.aembit.io/_astro/edge-api-quickstart-client-workload-form-github.DDF-5hmh_OXe8I.webp)

  3. Click **Save**.

     Aembit displays your new Client Workload in the list of Client Workloads.

* GitLab

  This Client Workload represents your GitLab CI job that authenticates with Aembit using the identity token.

  1. Switch back to the tab or window with your Aembit Tenant, go to **Client Workloads** in the left sidebar menu.

     Click **+ New**.

  2. Fill out the **Client Workload** form:

     * **Name** - Enter a descriptive name like `GitLab Edge API Test`.
     * **Client Identification** - Select **GitLab ID Token Project Path** and enter the project path (`<group>/edge-api-test`) you created earlier as the **Value**.

     ![Aembit Client Workload form](https://docs.aembit.io/_astro/edge-api-quickstart-client-workload-form-gitlab.Dsz3S4iJ_2B8fE.webp)

  3. Click **Save**.

     Aembit displays your new Client Workload in the list of Client Workloads.

### Create a Credential Provider

Next, you must create a Credential Provider. Credential Providers define the credential (like an API key or database password) for your target service that you’ll retrieve using Aembit Edge API calls later.

Follow these steps to create a Credential Provider in your Aembit Tenant:

> **Note**
>
> Regardless of which git provider you’re using, follow these steps to create a Credential Provider, as it applies to either use case.

1. While still in your Aembit Tenant, go to **Credential Providers** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Credential Provider** form:

3. Enter a **Name** for your Credential Provider, like `My Service API Key`.

4. Select `API Key` as the **Credential Type**.

5. Enter an **API Key** for your target service, like `my_secure_api_key_abc123xyz789`.

   ![Aembit Credential Provider form](https://docs.aembit.io/_astro/edge-api-quickstart-credential-provider-form.DlqVt5CA_ZPdAUm.webp)

6. Click **Save**.

   Aembit displays your new Credential Provider in the list of Credential Providers.

### Create a Server Workload

Now, you’ll create a Server Workload that represents the target service you want to retrieve credentials for. For the purposes of this quickstart guide, this won’t be a real service, but rather a placeholder to showcase how to authenticate and retrieve credentials using Aembit Edge API. Typically, this would be a database, API, or other service that your application needs to authenticate with. This Server Workload’s credential is what you’ll be retrieving later in this quickstart.

Follow these steps to create a Server Workload in your Aembit Tenant:

> **Note**
>
> Regardless of which git provider you’re using, follow these steps to create a Server Workload, as it applies to either use case.

1. While still in your Aembit Tenant, go to **Server Workloads** in the left sidebar menu.

   Click **+ New**.

2. Fill out the **Server Workload** form:

3. Enter a **Name** for your Server Workload, like `My API Service`.

4. In the **Service Endpoint** section, fill out the following fields:

   * **Host** - Enter the hostname of your target service, like `api.myservice.com`.
   * **Application Protocol** - Select `HTTP`.

   Record these values, as you’ll use them later when making API calls to retrieve credentials.

   Leave the remaining fields at their default values.

   ![Aembit Server Workload form](https://docs.aembit.io/_astro/edge-api-quickstart-server-workload-form.BJA9yr6p_ZbyQiQ.webp)

5. Click **Save**.

   Aembit displays your new Server Workload in the list of Server Workloads.

### Create an Access Policy

It’s time to put everything together into an Access Policy.

Access Policies define:

* the identity of your Client Workload through the Trust Provider
* which Client Workload (your CI/CD job) can access which Server Workload (your target service)
* the credential to retrieve for the Server Workload from the Credential Provider

You must create an Access Policy that defines these relationships, otherwise, Aembit won’t permit you to retrieve credentials.

Select the appropriate tab to follow the steps for creating an Access Policy for either GitHub or GitLab:

* GitHub

  1. While still in your Aembit Tenant, go to **Access Policies** in the left sidebar menu.

  2. Click **+ New** to open the Access Policy Builder.

  3. **Name the Access Policy**:

     1. In the **Name** field, enter a descriptive name for the policy.
     2. (Optional) In the **Description** field, add a description to help identify the policy’s purpose.
     3. Click **Save** to add these details to the policy.

  4. **Select a Client Workload**:

     1. In the **Client Workload** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the *GitHub Client Workload* you created earlier from the list.
     3. Click **Use Selected** to add the Client Workload to the policy.

  5. **Select a Server Workload**:

     1. In the **Server Workload** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the Server Workload you created earlier from the list.
     3. Click **Use Selected** to add the Server Workload to the policy.

  6. **Select a Trust Provider**:

     1. In the **Trust Provider** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the *GitHub ID Token Trust Provider* you created earlier from the list.
     3. Click **Use Selected** to add the Trust Provider to the policy.

  7. **Select a Credential Provider**:

     1. In the **Credential Provider** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the Credential Provider you created earlier from the list.
     3. Click **Use Selected** to add the Credential Provider to the policy.

  8. Click **Save Policy** to save the policy without activating it, or click **Save Policy & Activate** to save and activate it in one step.

     After saving, Aembit displays the Access Policy in the list of Access Policies.

     Click on the Access Policy, the detailed view should look similar to the following screenshot:

     ![Aembit Access Policies page detailed view](https://docs.aembit.io/_astro/edge-api-quickstart-access-policy-complete-github.CKgAfVHs_2jbs79.webp)

     You’ve successfully created an Access Policy that permits your GitHub ID Token-based Client Workload to retrieve credentials for your target service using Aembit Edge API.

* GitLab

  1. While still in your Aembit Tenant, go to **Access Policies** in the left sidebar menu.

  2. Click **+ New** to open the Access Policy Builder.

  3. **Name the Access Policy**:

     1. In the **Name** field, enter a descriptive name for the policy.
     2. (Optional) In the **Description** field, add a description to help identify the policy’s purpose.
     3. Click **Save** to add these details to the policy.

  4. **Select a Client Workload**:

     1. In the **Client Workload** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the *GitLab Client Workload* you created earlier from the list.
     3. Click **Use Selected** to add the Client Workload to the policy.

  5. **Select a Server Workload**:

     1. In the **Server Workload** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the Server Workload you created earlier from the list.
     3. Click **Use Selected** to add the Server Workload to the policy.

  6. **Select a Trust Provider**:

     1. In the **Trust Provider** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the *GitLab ID Token Trust Provider* you created earlier from the list.
     3. Click **Use Selected** to add the Trust Provider to the policy.

  7. **Select a Credential Provider**:

     1. In the **Credential Provider** card in the right panel, click **+ Configure**, then select the **Select Existing** tab.
     2. Select the Credential Provider you created earlier from the list.
     3. Click **Use Selected** to add the Credential Provider to the policy.

  8. Click **Save Policy** to save the policy without activating it, or click **Save Policy & Activate** to save and activate it in one step.

     After saving, Aembit displays the Access Policy in the list of Access Policies.

     Click on the Access Policy, the detailed view should look similar to the following screenshot:

     ![Aembit Access Policies page detailed view](https://docs.aembit.io/_astro/edge-api-quickstart-access-policy-complete-gitlab.DbIaYxko_1jFNEK.webp)

     You’ve successfully created an Access Policy that permits your GitLab ID Token-based Client Workload to retrieve credentials for your target service using Aembit Edge API.

You’re doing great and almost there! 💪

You’ve successfully:

* Connected Aembit to your chosen git provider (GitHub or GitLab)
* Created Aembit components and an Access Policy that ties them together

Continue to make your first API calls with the Aembit Edge API.

## Step 3: Make your first API calls

Your CI/CD job, Client Workload, and remaining Aembit components are now set up. You’re ready to make your first API calls to retrieve credentials for your target service.

> **Postman Collection**
>
> You can also use the
>
> [Aembit Edge API Postman Collection](https://docs.aembit.io/aembit-edge-api.postman_collection.json) to follow along if you prefer using Postman.

### Get your Aembit Edge API base URL

To make API calls to Aembit Edge API, you must have your Edge API base URL. Follow these steps to find it in your Aembit Tenant:

1. Hover over your username in the bottom left corner of your Aembit Tenant.

2. Select **Profile**.

3. Click the **Edge API Base URL** field to copy it. It should look something like this:

   ```shell
   https://<tenantId>.ec.useast2.aembit.io
   ```

   Where `<tenantId>` is your Aembit Tenant ID.

> **Note**
>
> When using Postman, make sure to remove the trailing slash (`/`) from the Edge API base URL.

### Authenticate your workload

Before retrieving credentials, you must authenticate your workload with Aembit Edge API, you’ll use the decoded identity token from the CI job you obtained earlier in [Step 1](#step-1-connect-aembit-to-github-or-gitlab).

First, authenticate your GitLab CI job with Aembit using your identity token:

* curl

  ```shell
  curl <your-edge-api-base-url>/edge/v1/auth \
    --request POST \
    --header 'Content-Type: application/json' \
    --data '{
    "clientId": <your-edge-sdk-client-id>,
    "client": {
        "<your-git-provider>": {
            "identityToken": "<your-decoded-identity-token>"
        }
    }
  }'
  ```

* Python

  ```python
  import http.client


  conn = http.client.HTTPSConnection("<tenantId>.aembit.io")


  payload = "{\n  \"clientId\": <your-edge-sdk-client-id>,\n  \"client\": {\n  <your-git-provider>: {\n    \"identityToken\": <your-decoded-identity-token>\n }\n  }\n}"


  headers = {
      'Content-Type': "application/json",
  }


  conn.request("POST", "/edge/v1/auth", payload, headers)


  res = conn.getresponse()
  data = res.read()


  print(data.decode("utf-8"))
  ```

* JavaScript

  ```javascript
  fetch('<your-edge-api-base-url>/edge/v1/auth', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      clientId: <your-edge-sdk-client-id>,
      client: {
        <your-git-provider>: {
          identityToken: <your-decoded-identity-token>
        }
      }
    })
  })
  ```

**Replace the values that apply:**

* `<tenantId>` with your Aembit Tenant ID
* `<your-edge-api-base-url>` with the Edge API base URL you copied earlier
* `<your-git-provider>` with either `github` or `gitlab` depending on your CI/CD job
* `<your-edge-sdk-client-id>` with the Client ID from your Trust Provider
* `<your-decoded-identity-token>` with your decoded GitHub or GitLab identity token (starts with `ey`)

> **Caution**
>
> As you may notice, this is different from most API calls where you would pass a `bearerToken` in the `Authorization` header. This is because Aembit Edge API uses the `identityToken` or JWT to verify the identity of your CI/CD job.
>
> If you get any unexpected 401 errors, double-check that you aren’t setting the `Authorization` header with a `Bearer` token. Instead, ensure you are passing the `identityToken` in the request body as the preceding examples show.

You’ll receive an access token response:

```json
{
  "accessToken": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "tokenType": "Bearer",
  "expiresIn": 3600
}
```

The `expiresIn` value is in seconds, so your access token is valid for 3600 seconds, or 1 hour.

Great! You’ve successfully authenticated with Aembit. 🔑

Use the value of `accessToken` as the `<your-access-token>` in the next step to get the credential for your target service.

### Get your credential

Now use the access token you just received to retrieve credentials for your target service. Select the appropriate tab to follow the steps for retrieving credentials for either GitHub or GitLab using the language of your choice:

* curl

  ```shell
  curl --location '<your-edge-api-base-url>/edge/v1/credentials' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <your-access-token>' \
  --data '{
      "client": {
          "<your-git-provider>": {
              "identityToken": "<your-decoded-identity-token>"
          }
      },
      "server": {
          "host": "<your-target-service-hostname>",
          "port": "80"
      },
      "credentialType": "ApiKey"
  }'
  ```

* Python

  ```python
  import http.client
  import json


  conn = http.client.HTTPSConnection("<your-edge-api-base-url>")
  payload = json.dumps({
    "client": {
      "<your-git-provider>": {
        "identityToken": "<your-decoded-identity-token>"
      }
    },
    "server": {
      "host": "<your-target-service-hostname>",
      "port": "80"
    },
    "credentialType": "ApiKey"
  })
  headers = {
    'Content-Type': 'application/json',
    'Authorization': 'Bearer <your-access-token>'
  }
  conn.request("POST", "/edge/v1/credentials", payload, headers)
  res = conn.getresponse()
  data = res.read()
  print(data.decode("utf-8"))
  ```

* JavaScript

  ```javascript
  const myHeaders = new Headers();
  myHeaders.append("Content-Type", "application/json");
  myHeaders.append("Authorization", "Bearer <your-access-token>");


  const raw = JSON.stringify({
    "client": {
      "<your-git-provider>": {
        "identityToken": "<your-decoded-identity-token>"
      }
    },
    "server": {
      "host": "<your-target-service-hostname>",
      "port": "80"
    },
    "credentialType": "ApiKey"
  });


  const requestOptions = {
    method: "POST",
    headers: myHeaders,
    body: raw,
    redirect: "follow"
  };


  fetch("<your-edge-api-base-url>/edge/v1/credentials", requestOptions)
    .then((response) => response.text())
    .then((result) => console.log(result))
    .catch((error) => console.error(error));
  ```

**Replace these values:**

* `<your-access-token>` with the token from the authentication response
* `<your-edge-api-base-url>` with the Edge API base URL you copied earlier
* `<your-git-provider>` with either `github` or `gitlab` depending on your CI/CD job
* `<your-decoded-identity-token>` with the decoded identity token you obtained earlier (starts with `ey`)
* `<your-target-service-hostname>` with the hostname of your target service

You’ll receive credentials for your configured service:

```json
{
  "credentialType": "ApiKey",
  "expiresAt": "2024-01-15T10:30:00Z",
  "data": {
    "apiKey": "someapikeythatsgreat!123"
  }
}
```

That’s it, you did it! 🎉🎉

You now have a valid API key to authenticate with your target service.

## Summary

In this quickstart guide, you learned how to:

* Connect Aembit to your GitHub or GitLab repository
* Create a Trust Provider to authenticate your CI/CD job
* Set up a CI/CD configuration file to generate an OIDC token
* Create Aembit components like Client Workload, Credential Provider, Server Workload, and Access Policy
* Authenticate your workload with Aembit Edge API
* Retrieve credentials for your target service using Aembit Edge API

Congratulations on completing your first Aembit Edge API integration! 🚀

## Common errors and solutions

### 401 unauthorized

```json
{
  "success": false,
  "message": "Authentication failed",
  "id": 12345
}
```

**Common causes**:

* Trust Provider not configured for your workload type
* *Invalid or expired* identity token
* Client Workload not matching configured patterns
* Incorrect EdgeSDK Client ID
* OIDC audience mismatch in GitLab CI configuration

**Solution**: Verify your Trust Provider configuration matches your workload environment and check that your EdgeSDK Client ID is correct.

### 400 bad request

```json
{
  "success": false,
  "message": "Invalid request or missing parameters",
  "id": 12346
}
```

**Common causes**:

* Missing required attestation data in the `client` object
* Invalid JSON structure
* Unsupported `credentialType`

**Solution**: Check that your request includes all required workload attestation data.

### 500 internal server error

```json
{
  "success": false,
  "message": "Internal server error",
  "id": 12347
}
```

**Common causes**:

* Credential Provider configuration issues
* Target service connectivity problems
* Temporary service unavailability

**Solution**: Check your Credential Provider configuration and try again after a brief delay.

> **What’s next?**
>
> Now that you’ve made your first successful API call:
>
> * **Explore the [API Reference](https://docs.aembit.io/dev-guide/api/edge/api-reference-edge)** for complete endpoint documentation
> * **See more about each endpoint** in the [Endpoints section](endpoints/overview.md)
> * **Set up additional workload types** like AWS Lambda or Kubernetes pods
> * **Configure different credential types** for other target services
> * **Use dynamic claims** in your OIDC tokens with [Dynamic Claims](../../../user-guide/access-policies/credential-providers/advanced-options/dynamic-claims.md)
> * **Implement error handling and retry logic** for production deployments
> * **Review the API reference** for detailed parameter specifications
