---
type: how-to
title: "Deploying to AWS ECS Fargate"
description: "How to deploy Aembit Edge Components in a ECS Fargate environment"
resource: https://docs.aembit.io/user-guide/deploy-install/serverless/aws-ecs-fargate/
interface: web-ui
tags: ["serverless", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Deploying to AWS ECS Fargate

Aembit provides different deployment options that you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality. The steps for each of these options, however, are specific to the deployment option you select.

This page describes the process to deploy Aembit Edge Components to ECS Fargate using Terraform.

To deploy Aembit Edge Components to your Kubernetes cluster, you must follow these steps:

1. [Add a Trust Provider](#step-1---add-a-trust-provider)
2. [Add an Agent Controller](#step-2---add-an-agent-controller)
3. [Modify and deploy terraform configuration](#step-3---modify-and-deploy-terraform-configuration)

To further customize your deployments, see the available [optional configurations](#configuration-variables).

## Before you begin

1. Ensure that Terraform has valid AWS credentials to deploy resources. Terraform doesn’t require the AWS CLI but can use its credentials if available. Terraform automatically looks for credentials in environment variables, AWS credentials files, IAM roles, and other sources.

   For details on configuring authentication, refer to the [AWS Provider Authentication Guide](https://registry.terraform.io/providers/hashicorp/aws/latest/docs#authentication-and-configuration).

2. Verify that you have initialized Terraform and that you have the required permissions to execute the deployment. Go to your Terraform deployment directory for the Client Workload and run the following command:

   ```shell
   terraform plan
   ```

   The command should complete without errors.

## Step 1 - Add a Trust Provider

You need to create a Trust Provider, or use an existing one, to enable the Agent Controller (created in the next step) to authenticate with the Aembit cloud. This Trust Provider relies on the AWS Role associated with your application for authentication.

1. Log into your Aembit Tenant and go to **Edge Components —> Trust Providers**.

2. Click **+ New**, revealing the **Trust Provider** pop out.

3. Enter a **Name** and optional **Description**.

4. Select **AWS Role** as the **Trust Provider**.

5. Under **Match Rules**, click **+ New Rule** and set the following:

   1. **Attribute** - Select **accountId**

   2. **Value** - Enter the AWS account ID (without dashes) where your Client Workload is running

6. Click **Save**.

   ![Add Trust Provider UI](https://docs.aembit.io/_astro/create-trust-provider-ecs-fargate.CDnogrX9_ZDSpz.webp)

## Step 2 - Add an Agent Controller

1. Log into your Aembit Tenant and go to **Edge Components —> Agent Controllers**.

2. Click **+ New**, revealing the **Agent Controller** pop out.

3. Enter a **Name** and optional **Description**.

4. Select the **Trust Provider** you created in [Step 1](#step-1---add-a-trust-provider).

5. Click **Save**.

   ![Add Agent Controller UI](https://docs.aembit.io/_astro/create-agent-controller-ecs-fargate.BT1VWuxS_Ffce1.webp)

## Step 3 - Modify and deploy Terraform configuration

1. Add the Aembit Edge ECS Module to your Terraform code, using configuration:

   ```hcl
   module "aembit-ecs" {
     source  = "Aembit/ecs/aembit"
     version = "<version>" # Find the latest version at https://registry.terraform.io/modules/Aembit/ecs/aembit/latest


     aembit_tenantid = "<Aembit_Tenant_ID>"
     aembit_agent_controller_id = "<Aembit_Agent_Controller_ID>"


     ecs_cluster = "<ECS_Fargate_cluster_name>"
     ecs_vpc_id = "<ECS_Fargate_cluster_VPC_id>"
     ecs_subnets = ["<ECS_Fargate_cluster_subnet_1>","<ECS_Fargate_cluster_subnet_2>","<ECS_Fargate_cluster_subnet_3>"]
     ecs_security_groups = ["<Security_group_for_Agent_Controller>"]
   }
   ```

   > **Note**
   >
   > To see additional configuration options, see [Optional configurations](#configuration-variables)

2. Add the Aembit Agent Proxy container definition to your Client Workload Task Definitions.

   The following code sample shows an example of this by injecting `jsondecode(module.aembit-ecs.agent_proxy_container)` as the first container of the Task definition for your Client Workload.

   ```hcl
   resource "aws_ecs_task_definition" "workload_task" {
     family                = "workload_task"
     container_definitions = jsonencode([
       jsondecode(module.aembit-ecs.agent_proxy_container),
       {
         name = "workload"
         ...
       }])
   ```

3. Add the required explicit steering environment variables to your Client Workload Task Definitions. For example:

   ```hcl
   environment = [
     {"name": "http_proxy", "value": module.aembit-ecs.aembit_http_proxy},
     {"name": "https_proxy", "value": module.aembit-ecs.aembit_https_proxy}
   ]
   ```

4. Execute `terraform init` to download Aembit ECS Fargate module.

5. With your Terraform code updated as described, run `terraform apply` or your typical Terraform configuration scripts to deploy Aembit Edge into your AWS ECS Client Workloads.

## Configuration variables

The following table lists the configurable variables of the module and their default values.

*All variables are required unless marked* Optional.

### `aembit_tenantid`

Default - not set

The Aembit TenantID with which to associate this installation and Client Workloads.

***

### `aembit_agent_controller_id`

Default - not set

The Aembit Agent Controller ID with which to associate this installation.

***

### `aembit_trusted_ca_certs`

Optional Default - not set

Additional CA Certificates that the Aembit AgentProxy should trust for Server Workload connectivity.

***

### `ecs_cluster`

Default - not set

The AWS ECS Cluster that hosts the Aembit Agent Controller.

***

### `ecs_vpc_id`

Default - not set

The AWS VPC that provides network connectivity for the Aembit Agent Controller. This must be the same VPC as your Client Workload ECS Tasks.

***

### `ecs_subnets`

Default - not set

The subnets that the Aembit Agent Controller and Agent Proxy containers use for connectivity between Proxy and Controller and Aembit Cloud.

***

### `ecs_security_groups`

Default - not set

The security group to assign to the AgentController service. This security group must allow inbound HTTP access from the AgentProxy containers running in your Client Workload ECS Tasks.

***

### `agent_controller_task_role_arn`

Default - `arn:aws:iam::${data.aws_caller_identity.current.account_id}:role/ecsTaskExecutionRole`

The AWS IAM Task Role to use for the Aembit AgentController Service container. AgentController uses this role to register with the Aembit Cloud Service.

***

### `agent_controller_execution_role_arn`

Default - `arn:aws:iam::${data.aws_caller_identity.current.account_id}:role/ecsTaskExecutionRole`

The AWS IAM Task Execution Role used by Amazon ECS and Fargate agents for the Aembit AgentController Service.

***

### `log_group_name`

Optional Default - `/aembit/edge`

Specifies the name of an optional log group to create and send logs to for components created by this module. You can set this value to `null`.

***

### `agent_controller_image`

Default - not set

The container image to use for the AgentController installation. As a best practice, always [verify container image signatures](../verify-container-images.md#agent-controller).

***

### `agent_proxy_image`

Default - not set

The container image to use for the AgentProxy installation. As a best practice, always [verify container image signatures](../verify-container-images.md#agent-proxy).

***

### `aembit_stack`

Default - `useast2.aembit.io`

The Aembit Stack which hosts the specified Tenant.

***

### `ecs_task_prefix`

Default - `aembit_`

Prefix to include in front of the Agent Controller ECS Task Definitions to ensure uniqueness.

***

### `ecs_service_prefix`

Default - `aembit_`

Prefix to include in front of the Agent Controller Service Name to ensure uniqueness.

***

### `ecs_private_dns_domain`

Default - `aembit.local`

The Private DNS TLD that the specified AWS VPC uses for AgentProxy to AgentController connectivity.

***

### `agent_proxy_resource_set_id`

Default - not set

Associates Agent Proxy with a specific [Resource Set](../../administration/resource-sets/overview.md)

***

### `agent_controller_environment_variables`

Default - not set

Set [Agent Controller Environment Variables](../../../reference/edge-components/edge-component-env-vars.md#agent-controller-environment-variables) directly.

***

### `agent_proxy_environment_variables`

Default - not set

Set [Agent Proxy Environment Variables](../../../reference/edge-components/edge-component-env-vars.md#agent-proxy-environment-variables) directly.

## Overriding Agent Controller and Agent Proxy environment variables

Use the `agent_controller_environment_variables` and `agent_proxy_environment_variables` Terraform module variables to set the respective Edge Component [environment variables](../../../reference/edge-components/edge-component-env-vars.md).

```hcl
  agent_controller_environment_variables = {
      "AEMBIT_LOG_LEVEL": "debug"
  }
  agent_proxy_environment_variables = {
      "AEMBIT_LOG_LEVEL": "debug"
  }
```
