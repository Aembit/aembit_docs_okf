---
type: how-to
title: "Create a GitLab Service Account Integration for a GitLab.com plan"
description: "How to create a GitLab Service Account Credential Provider Integration using a GitLab.com plan"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/integrations/gitlab/
interface: web-ui
tags: [integration, credential-provider, access-policy]
timestamp: 2025-10-08T09:42:10-07:00
type_inferred: true
---

# Create a GitLab Service Account Integration for a GitLab.com plan

The GitLab Service Account Credential Provider Integration allows you to create a [Managed GitLab Account Credential Provider](../managed-gitlab-account.md), which provides credential lifecycle management and rotation capabilities for secure authentication between your GitLab instances and other Client Workloads. This page details everything you need to create a GitLab Service Account Credential Provider Integration.

GitLab Free tier limitation

Service accounts are only available for GitLab *Premium and Ultimate* subscription tiers. If you’re using a Free tier subscription, consider upgrading to a paid plan or using a [GitLab Dedicated or Self-Managed instance](gitlab-dedicated-self.md) instead.

This integration requires the use of two types of GitLab accounts:

* **GitLab Administrator account** in a top-level-group with the `Owner` role. This administrator account performs the initial authorization for the Aembit Credential Provider Integration to start communicating with GitLab.

* **GitLab Service Account** that the preceding GitLab Administrator account eventually creates. This service account performs credential lifecycle management for the Managed GitLab Account Credential Provider.

See [How the GitLab Service Account integration works](overview.md#gitlab-service-account-integration) for more details.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

* `Owner` role access to [GitLab Admin area](https://docs.gitlab.com/administration/admin_area/) and [REST API](https://docs.gitlab.com/api/rest/)

* A [GitLab Personal Access Token (PAT)](https://docs.gitlab.com/user/profile/personal_access_tokens/) for your [GitLab service account](https://docs.gitlab.com/user/profile/service_accounts/) with the `Owner` role as well as `api` and `self_rotate` [scopes](https://docs.gitlab.com/user/profile/personal_access_tokens/#personal-access-token-scopes)

## Configure a GitLab service account integration

[Section titled “Configure a GitLab service account integration”](#configure-a-gitlab-service-account-integration)

To create a GitLab service account integration, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers -> Integrations** in the left sidebar.

   ![Credential Provider - Integrations tab](https://docs.aembit.io/_astro/cp-integrations-page.Q7suvjMH_Z1SCKgo.webp)

2. (Optional) In the top right corner, select the [Resource Set](../../../administration/resource-sets/overview.md) that you want this Credential Provider Integration to reside.

3. Click **+ New**, which displays the **Integration** pop out menu.

4. Select **GitLab Service Account**, and enter a **Display Name** and optional **Description**.

5. Fill out the remaining fields:

   * **Token Endpoint URL** - Enter `https://gitlab.com`, indicating that you’re using a GitLab.com plan.

     See [GitLab subscriptions](overview.md#gitlab-subscriptions) for more details.

   * **Top Level Group ID** - Enter the numeric ID of the top-level group that contains your GitLab service account.\
     See GitLab’s [Find the Group ID](https://docs.gitlab.com/user/group/#find-the-group-id) for more details.

     GitLab Free tier limitation

     Service accounts are only available for GitLab *Premium and Ultimate* subscription tiers. If you’re using a Free tier subscription, consider upgrading to a paid plan or using a [GitLab Dedicated or Self-Managed instance](gitlab-dedicated-self.md) instead.

   * **Personal Access Token** - Enter the Personal Access Token that’s associated with your GitLab Service Account.

     If you don’t already have a GitLab service account with a PAT, see [Create a GitLab service account and PAT](#create-a-gitlab-service-account-and-pat).

   The form should look similar to the following screenshot:

   ![Completed GitLab Service Account Credential Provider Integration](https://docs.aembit.io/_astro/cp-integration-gitlab.com.BsMWh2iK_Z1zg0BA.webp)

6. Click **Save**.

   Aembit displays the new integration in the list of Credential Provider Integrations.

   Security best practice

   As soon as you successfully create the integration, Aembit rotates the token for the GitLab service account and continues to rotate it as long as the Credential Provider Integration exists.

## Create a GitLab service account and PAT

[Section titled “Create a GitLab service account and PAT”](#create-a-gitlab-service-account-and-pat)

The service account you use for the GitLab Service Account Credential Provider Integration must be in a top-level group with the `Owner` role to have access to GitLab APIs.

GitLab Free tier limitation

Service accounts are only available for GitLab *Premium and Ultimate* subscription tiers. If you’re using a Free tier subscription, consider upgrading to a paid plan or using a [GitLab Dedicated or Self-Managed instance](gitlab-dedicated-self.md) instead.

To create a GitLab service account and PAT, use either the GitLab UI or GitLab API:

* GitLab UI

  1. Follow GitLab’s documentation to [Create a Service Account using the GitLab UI](https://docs.gitlab.com/user/profile/service_accounts/?tab=Instance-wide+service+accounts#view-and-manage-service-accounts).

  2. Follow GitLab’s documentation to [Create a Personal Access Token](https://docs.gitlab.com/user/profile/personal_access_tokens/#create-a-personal-access-token) for the service account you just created.\
     **Ensure that you select the following scopes**:

     * `api`
     * `self_rotate`

  3. **Copy the token value and store it** in a secure location as you won’t be able to view it again.

  4. Use this token to [create the GitLab Service Account Credential Provider Integration](#configure-a-gitlab-service-account-integration) in your Aembit Tenant.

* GitLab API

  You must perform the following steps using your GitLab Admin account that has `Owner` role access to a top-level group.

  You’ll also need your numerical top-level group ID. Follow GitLab’s documentation to [Find the Group ID](https://docs.gitlab.com/user/group/#find-the-group-id).

  1. *From your terminal*, enter the following command to create the GitLab service account you want to associate with the integration. Make sure to replace:

     * `<GITLAB_TOKEN>` with your GitLab Admin account’s Personal Access Token
     * `<numeric_group_id>` with your top-level group ID See [Find the Group ID](https://docs.gitlab.com/user/group/#find-the-group-id) for more details
     * For `<sa_name>` and `<sa_username>`, enter values that follow your organization’s patterns

     ```shell
     curl --header "PRIVATE-TOKEN: <GITLAB_TOKEN>" \
       -X POST "https://gitlab.com/api/v4/groups/<numeric_group_id>/service_accounts" \
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
     * `<numeric_group_id>` with your top-level group ID
     * `<service_account_user_id>` with the `id` you recorded from the previous step
     * For `<sa_name>`, enter a value that follows your organization’s patterns

     ```shell
     curl --header "PRIVATE-TOKEN: <GITLAB_TOKEN>" \
       -X POST "https://gitlab.com/api/v4/groups/<numeric_group_id>/service_accounts/<service_account_user_id>/personal_access_tokens" \
       --data "name=<sa_username>" \
       --data "scopes[]=api" \
       --data "scopes[]=self_rotate"
     ```

     If successful, the response should look similar to the following:

     ```shell
     {"id":1234,"name":"<token_name>","revoked":false,"created_at":"2025-03-21T20:18:23.333Z","description":null,"scopes":["api","self_rotate"],"user_id":<service_account_user_id>,"last_used_at":null,"active":true,"expires_at":"2025-03-31","token":"<token>"}
     ```

     Record the `token` value as you’ll need it in the final step.

  3. Add the new service account you just created to your top-level group:

     Make sure to replace:

     * `<GITLAB_TOKEN>` with your GitLab API access token
     * `<numeric_group_id>` with your top-level group ID
     * `<service_account_user_id>` with the `id` you recorded earlier

     ```shell
     curl --header "PRIVATE-TOKEN: <GITLAB_TOKEN>" \
       -X POST "https://gitlab.com/api/v4/groups/<numeric_group_id>/members" \
       --data "user_id=<service_account_user_id>" \
       --data "access_level=50"
     ```

## Additional resources

[Section titled “Additional resources”](#additional-resources)

* [Managed GitLab Account](../managed-gitlab-account.md)
* [Credential Provider Integrations overview](overview.md)
* [GitLab Dedicated/Self-Managed integration](gitlab-dedicated-self.md)
