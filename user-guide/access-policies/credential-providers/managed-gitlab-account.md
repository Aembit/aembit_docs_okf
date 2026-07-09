---
type: how-to
title: "Configure a Managed GitLab Account Credential Provider"
description: "How to create and use a Managed GitLab Account Credential Provider"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/managed-gitlab-account/
interface: web-ui
tags: [credential-provider, access-policy]
timestamp: 2025-10-08T09:42:10-07:00
type_inferred: true
---

# Configure a Managed GitLab Account Credential Provider

The Manage GitLab Account Credential Provider uses the [GitLab Service Account Credential Provider Integration](integrations/overview.md#gitlab-service-account-integration) to allow you to manage the credential lifecycle of your GitLab service accounts.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

You must have the following to create a Managed GitLab Account Credential Provider:

* A completed [GitLab Service Account Credential Provider Integration](integrations/gitlab.md)

## Create a Managed GitLab account Credential Provider

[Section titled “Create a Managed GitLab account Credential Provider”](#create-a-managed-gitlab-account-credential-provider)

To create a Managed GitLab Account Credential Provider, follow these steps:

1. Log into your Aembit Tenant, and go to **Credential Providers** in the left sidebar.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that you want this Credential Provider to reside.

3. Click **+ New**, which displays the Credential Provider pop out menu.

4. Enter a **Name** and optional **Description**.

5. Under **Credential Type**, select **Managed GitLab Account**, revealing more fields.

6. Fill out the remaining fields:

   1. **Select GitLab Integration** - Select a GitLab Service Account integration you’ve already configured.

      Note

      If the **Select GitLab Integration** dropdown menu is empty, you either:

      * May not have any GitLab Service Account integrations configured yet. See [GitLab Service Account](integrations/gitlab.md) to create one.

      * May need to change Resource Sets.

   2. **GitLab Group IDs or Paths** - Enter the [group ID](https://docs.gitlab.com/user/group/#access-a-group-by-using-the-group-id) or [group path](https://docs.gitlab.com/user/namespace/#determine-which-type-of-namespace-youre-in). If entering more than one, separate them with commas (for example: `parent-group/subgroup,34,56`).

   3. **GitLab Project IDs or Paths** - Enter the [project ID](https://docs.gitlab.com/user/project/working_with_projects/#access-a-project-by-using-the-project-id) or project path. If entering more than one, separate them with commas (`my-project.345678,my-other-project`).

   4. **Access Level** - Enter the [GitLab Access Level](https://docs.gitlab.com/api/access_requests/#valid-access-levels) you want your GitLab service account to have.

   5. **Scope** - Enter the [GitLab Personal Access Token (PAT) Scopes](https://docs.gitlab.com/user/profile/personal_access_tokens/#personal-access-token-scopes) you want the GitLab service account to have. When entering more than one, separate them with spaces (for example: `api read_user k8s_proxy`).

   6. **Lifetime** - Enter the number of days you want credentials to remain active.

   The form should look similar to the following screenshot:

   ![Completed Manage GitLab Account Credential Provider form](https://docs.aembit.io/_astro/cp-managed-gitlab-account.DFKEvpyX_ZN7C3S.webp)

7. Click **Save**.

   Aembit displays the new Credential Provider in the list of Credential Providers.

## Verify the Credential Provider

[Section titled “Verify the Credential Provider”](#verify-the-credential-provider)

To verify that you successfully created the Managed GitLab Account Credential Provider and it’s communicating with GitLab:

1. In your Aembit Tenant, go to **Credential Providers**.

2. (Optional) In the top right corner, select the [Resource Set](../../administration/resource-sets/overview.md) that your Credential Provider resides.

3. Select your newly created Credential Provider.

   Scroll down to see all the details provided by GitLab for this Service Account.

   You should see something similar to the following screenshot:

   ![Completed Managed GitLab Account Credential Provider with 'Ready' badge](https://docs.aembit.io/_astro/cp-integration-gitlab-sa-ready.dTYtBe-t_Z1gfD7F.webp)

### (Optional) Verify in the GitLab Admin area

[Section titled “(Optional) Verify in the GitLab Admin area”](#optional-verify-in-the-gitlab-admin-area)

To verify that the Managed GitLab Account Credential Provider successfully creates service account in GitLab:

1. Log into your *administrator* GitLab account associated with your GitLab Service Account integration.

2. Go to **Admin area -> Overview -> Users**.

3. Select the service account formatted like this: `Aembit_<credential_provider_name>_managed_service_account`.

4. On the **Account** tab, verify that the **Username** and **ID** match the values shown in the Credential Provider in the Aembit UI.

   Similar to the following screenshot:

   ![GitLab Admin area UI - Groups and projects tab on service account](https://docs.aembit.io/_astro/cp-integration-gitlab-sa-gl-account.C0IevCb3_2rSvGd.webp)

5. On the **Groups and projects** tab, verify that the groups, projects, and access levels match what you entered in the Managed GitLab Account form. GitLab displays these in a table showing Groups with their associated Projects and Access Levels.

   Similar to the following screenshot:

   ![GitLab Admin area UI - Accounts tab on service account](https://docs.aembit.io/_astro/cp-integration-gitlab-sa-gl-groups-projects.DuvuiAjT_ZYIYm6.webp)
