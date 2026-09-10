---
type: how-to
title: "Create a GitLab Service Account Integration for a Dedicated/Self-Managed instance"
description: "How to create a GitLab Service Account Credential Provider Integration using a GitLab Dedicated or Self-Managed instance"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/integrations/gitlab-dedicated-self/
interface: web-ui
tags: ["integration", "credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Create a GitLab Service Account Integration for a Dedicated/Self-Managed instance

The GitLab Service Account Credential Provider Integration allows you to create a [Managed GitLab Account Credential Provider](../managed-gitlab-account.md), which provides credential lifecycle management and rotation capabilities for secure authentication between your GitLab instances and other Client Workloads. This page details everything you need to create a GitLab Service Account Credential Provider Integration.

This integration requires the use of two types of GitLab accounts:

* **GitLab Administrator account**. This administrator account performs the initial authorization for the Aembit Credential Provider Integration to start communicating with GitLab.

* **GitLab Service Account** that the preceding GitLab Administrator account eventually creates. This service account performs credential lifecycle management for the Managed GitLab Account Credential Provider.

See [How the GitLab Service Account integration works](overview.md#gitlab-service-account-integration) for more details.

## Prerequisites

* Administrator access to [GitLab Admin area](https://docs.gitlab.com/administration/admin_area/) and the GitLab [REST API](https://docs.gitlab.com/api/rest/)

* A [GitLab Personal Access Token (PAT)](https://docs.gitlab.com/user/profile/personal_access_tokens/) for your [GitLab service account](https://docs.gitlab.com/user/profile/service_accounts/) with `api` and `self_rotate` [scopes](https://docs.gitlab.com/user/profile/personal_access_tokens/#personal-access-token-scopes)

* The URL of your GitLab Dedicated or GitLab Self-Managed instance (see [GitLab’s plans](https://docs.gitlab.com/subscriptions/choosing_subscription/) for details)\
  For example: `gitlab_tenant_name.gitlab-dedicated.com` or `https://gitlab.my-company.com`

## Configure a GitLab service account integration

To create a GitLab service account integration, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers -> Integrations** in the left sidebar.

   ![Credential Provider - Integrations tab](https://docs.aembit.io/_astro/cp-integrations-page.Q7suvjMH_Z1SCKgo.webp)

2. (Optional) In the top right corner, select the [Resource Set](../../../administration/resource-sets/overview.md) that you want this Credential Provider Integration to reside.

3. Click **+ New**, which displays the **Integration** pop out menu.

4. Select **GitLab Service Account**, and enter a **Display Name** and optional **Description**.

5. Fill out the remaining fields:

   * **Token Endpoint URL** - Enter the URL of your GitLab Dedicated or GitLab Self-Managed instance.

     See [GitLab subscriptions](overview.md#gitlab-subscriptions) for more details.

   * **Top Level Group ID** - n/a\
     Aembit disables this field when using GitLab Dedicated or Self-Managed instance URLs.

   * **Personal Access Token** - Enter the GitLab Personal Access Token that’s associated with your instance-level Administrator service account that must have `api` and `self_rotate` scopes.

     If you don’t already have a GitLab service account with a PAT, see [Create a GitLab service account and PAT](#create-a-gitlab-service-account-and-pat).

   The form should look similar to the following screenshot:

   ![Completed GitLab Service Account Credential Provider Integration](https://docs.aembit.io/_astro/cp-integration-gitlab-sa.D5sEZiCq_ZRQw1q.webp)

6. Click **Save**.

   Aembit displays the new integration in the list of Credential Provider Integrations.

   > **Security best practice**
   >
   > As soon as you successfully create the integration, Aembit rotates the token for the GitLab service account and continues to rotate it as long as the Credential Provider Integration exists.

## Create a GitLab service account PAT

To create a GitLab service account PAT, you must have *Administrator* access to your GitLab Admin area and GitLab APIs.

This process has two main parts:

1. [Create a PAT for your GitLab Administrator account](#create-a-gitlab-administrator-account-pat) using the *GitLab UI*.

2. [Create a GitLab service account and PAT](#create-a-gitlab-service-account-and-pat) using either the *GitLab UI* or *GitLab API*.

### Create a GitLab Administrator account PAT

To create a PAT for your GitLab Administrator account, follow these steps:

1. Log into your GitLab Admin area with an Administrator user account.

2. See [Create a personal access token](https://docs.gitlab.com/user/profile/personal_access_tokens/#create-a-personal-access-token) in the GitLab docs to create a PAT for your *Administrator user account* (not the service account).

3. Keep the GitLab Admin area UI open, as you need it in the next step.

### Create a GitLab service account and PAT

To create a GitLab service account and PAT, use either the GitLab UI or GitLab API:

* GitLab UI

  1. Follow GitLab’s documentation to [Create a Service Account using the GitLab UI](https://docs.gitlab.com/user/profile/service_accounts/?tab=Instance-wide+service+accounts#create-a-service-account).

  2. Follow GitLab’s documentation to [Create a Personal Access Token](https://docs.gitlab.com/user/profile/personal_access_tokens/#create-a-personal-access-token) for the service account you just created.\
     **Ensure that you select the following scopes**:

     * `api`
     * `self_rotate`

  3. **Copy the token value and store it** in a secure location as you won’t be able to view it again.

  4. Use this token to [create the GitLab Service Account Credential Provider Integration](#configure-a-gitlab-service-account-integration) in your Aembit Tenant.

* GitLab API

  You must perform the following steps using your GitLab Admin account that has Administrator access to your GitLab instance.

  1. *From your terminal*, enter the following command to create the GitLab service account you want to associate with the integration. Make sure to replace `<GITLAB_TOKEN>` with your GitLab Admin account’s Personal Access Token and `<GITLAB_URL>` with your GitLab instance URL. For `<sa_name>` and `<sa_username>`, enter values that follow your organization’s patterns.

     ```shell
     curl --header "PRIVATE-TOKEN: <GITLAB_TOKEN>" \
       -X POST "<GITLAB_URL>/api/v4/service_accounts" \
       --data "name=<sa_name>" \
       --data "username=<sa_username>"
     ```

     If successful, the response should look similar to the following:

     ```shell
     {"id":12345678,"username":"my-service-account","name":"my-service-account","email":"mysa@example.com"}
     ```

     The `id` is the user ID of the Service Account. Record this `id`, as you’ll need it in the next step.

  2. Create a PAT for the GitLab service account you just created.

     Make sure to replace:

     * `<GITLAB_TOKEN>` with your GitLab Admin account’s Personal Access Token
     * `<GITLAB_URL>` with your GitLab instance URL
     * `<service_account_user_id>` with the `id` you recorded from the previous step
     * For `<token_name>`, enter a value that follows your organization’s patterns

     ```shell
     curl --header "PRIVATE-TOKEN: <GITLAB_TOKEN>" \
       -X POST "<GITLAB_URL>/api/v4/users/<service_account_user_id>/personal_access_tokens" \
       --data "scopes[]=api" \
       --data "scopes[]=self_rotate" \
       --data "name=<token_name>"
     ```

     If successful, the response should look similar to the following:

     ```shell
     {"id":1234,"name":"<token_name>","revoked":false,"created_at":"2025-03-21T20:18:23.333Z","description":null,"scopes":["api","self_rotate"],"user_id":<service_account_user_id>,"last_used_at":null,"active":true,"expires_at":"2025-03-31","token":"<token>"}
     ```

     Record the `token` value as you’ll need it in the final step.

  3. Use the token to [create the GitLab Service Account Credential Provider Integration](#configure-a-gitlab-service-account-integration) in your Aembit Tenant.

## Additional resources

* [Managed GitLab Account](../managed-gitlab-account.md)
* [Credential Provider Integrations overview](overview.md)
* [GitLab.com integration](gitlab.md)
