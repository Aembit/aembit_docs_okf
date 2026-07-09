---
type: tutorial
title: "Quickstart: Aembit core setup"
description: "Aembit's quickstart core guide - practical experience automating and securing access between workloads"
resource: https://docs.aembit.io/get-started/quickstart/quickstart-core/
tags: [quickstart]
timestamp: 2026-06-30T13:30:29-04:00
type_inferred: true
---

# Quickstart: Aembit core setup

Aembit is a cloud-native, non-human identity and access management platform. It provides secure, seamless access management for workloads across diverse environments. It simplifies how organizations control and authorize access between client and Server Workloads**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../concepts/server-workloads.md), ensuring that only the right workloads can access critical resources at the right time.

Aembit shifts the focus away from long-term credential management by enabling automated, secure access management for workloads connecting to services. By concentrating on managing access rather than secrets, Aembit provides a flexible and security-first approach to non-human identity across a wide range of infrastructures.

## In this guide

[Section titled “In this guide”](#in-this-guide)

This quickstart guide provides a practical introduction to Aembit’s capabilities. Here’s what you’ll do:

1. Set up a sandbox environment with pre-configured client and Server Workloads using Docker Desktop with Kubernetes.

2. Deploy workloads and configure a secure Access Policy between the client and server.

3. Gain practical experience managing automated, secure access between workloads.

**Estimated Time to Complete** - \~15 minutes (if prerequisites are already installed).

By completing this quickstart guide, you’ll have practical experience creating an example of Aembit’s capabilities, ensuring quick results as you implement access management in a real-world environment. Once you are comfortable with these foundational steps, Aembit offers the flexibility to manage access for more complex and scalable workloads across a range of infrastructure setups.

## Before you begin

[Section titled “Before you begin”](#before-you-begin)

Before starting Aembit’s quickstart guide, you must complete the following prerequisites:

1. [Sign up with Aembit](#sign-up-with-aembit) and you can access your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../concepts/administration.md) at `https://<tenant-ID>.aembit.io`.

2. [Install Docker Desktop and enable Kubernetes](#install-docker-desktop-and-enable-kubernetes).

3. [Install Helm](#install-helm).

Note

The Aembit quickstart guide doesn’t require complex network configurations, such as a static external IP, outbound connection adjustments, or firewall rule changes. Aembit has designed these prerequisites to work securely and seamlessly within your local environment.

### Sign up with Aembit

[Section titled “Sign up with Aembit”](#sign-up-with-aembit)

Visit the [Sign Up page](https://useast2.aembit.io/signup) to create an account and set up your tenant for accessing the platform.

A Tenant in Aembit is your organization’s dedicated workspace within the platform. It isolates your workloads, Access Policies**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md), and configurations, enabling you to manage your environment securely and efficiently.

Your Aembit Tenant ID is a unique identifier for your workspace, which you must use to access your Aembit Tenant at `https://<tenant-ID>.aembit.io`.

Look for a welcome email from Aembit. It may take a few minutes; check your Junk or Spam folders if you don’t see it.

### Install Docker Desktop and enable Kubernetes

[Section titled “Install Docker Desktop and enable Kubernetes”](#install-docker-desktop-and-enable-kubernetes)

Docker Desktop includes Docker Engine and Kubernetes, making it easier to manage your containerized applications.

1. Download and install Docker Desktop from the [official Docker website](https://docs.docker.com/get-started/get-docker/) for your operating system.

   Once installed, open Docker Desktop.

2. Enable Kubernetes by going to **Settings -> Kubernetes** in Docker Desktop and toggling the **Enable Kubernetes** switch to the **On** position.

   ![Enable Kubernetes in Docker](https://docs.aembit.io/_astro/quickstart_enable_kubernetes.B1yxdwOD_Z1x3sXB.webp)

   Security best practice

   If you get errors or warnings about permissions on your `~/.kube/config` file being too permissive, tighten up the file’s permissions by running the following command:

   ```shell
   chmod 600 ~/.kube/config
   ```

   Locking down permissions on your `~/.kube/config` file is a security best practice since the config file contains sensitive credentials for accessing Kubernetes clusters.

### Install Helm

[Section titled “Install Helm”](#install-helm)

Helm deploys the pre-configured sandbox client and Server Workloads for this quickstart guide. A basic understanding of [Helm commands](https://helm.sh/docs/helm/) is helpful for deploying the sandbox workloads.

Select one of the following tabs for your operating system to install Helm:

* Windows

  1. Download the [latest Helm version](https://github.com/helm/helm/releases) for Windows.

  2. Run the installer and follow the on-screen instructions.

  3. Once installed, open a Command Prompt or PowerShell terminal and verify the installation by running:

     ```cmd
     helm version
     ```

     **Expected Output:**

     ```cmd
     version.BuildInfo{Version:"v3.x.x", GitCommit:"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", GitTreeState:"clean", GoVersion:"go1.x.x"}
     ```

* macOS

  1. Use Homebrew to install Helm:

     ```shell
     brew install helm
     ```

  2. Verify the installation:

     ```shell
     helm version
     ```

     **Expected Output:**

     ```shell
     version.BuildInfo{Version:"v3.x.x", GitCommit:"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", GitTreeState:"clean", GoVersion:"go1.x.x"}
     ```

* Linux

  1. Download and install the latest Helm binary:

     ```shell
     curl -fsSL -o get_helm.sh "https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3"
     chmod 700 get_helm.sh
     ./get_helm.sh
     ```

  2. Verify the installation:

     ```shell
     helm version
     ```

     **Expected Output:**

     ```shell
     version.BuildInfo{Version:"v3.x.x", GitCommit:"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx", GitTreeState:"clean", GoVersion:"go1.x.x"}
     ```

With these prerequisites complete, you are ready to deploy the sandbox workloads and configure secure access between workloads.

## Deploying workloads

[Section titled “Deploying workloads”](#deploying-workloads)

Make sure that your environment is ready for deployment by verifying the following:

* [Docker Desktop installed and Kubernetes enabled](#install-docker-desktop-and-enable-kubernetes).

* [Helm installed and configured correctly](#install-helm).

With these steps in place, you are ready to deploy the workloads.

### Install applications

[Section titled “Install applications”](#install-applications)

Re-installing? Clean up first

If you’ve run this quickstart before, remove the previous deployment before reinstalling to avoid conflicts. **Skip this step if this is your first time.**

```shell
helm uninstall aembit-quickstart -n aembit-quickstart
helm uninstall aembit -n aembit


kubectl delete namespace aembit-quickstart
kubectl delete namespace aembit


helm repo remove aembit
```

1. From your terminal, add the Aembit Helm chart repo by running:

   ```shell
   helm repo add aembit https://helm.aembit.io
   ```

2. Deploy both the client and Server Workloads:

   ```shell
   helm install aembit-quickstart aembit/quickstart \
     -n aembit-quickstart \
     --create-namespace
   ```

### Set up ingress routing

[Section titled “Set up ingress routing”](#set-up-ingress-routing)

Docker Desktop v4.38 and later use `kind` (Kubernetes in Docker) as the default Kubernetes provisioner. Under `kind`, the sandbox’s services don’t bind to `localhost`, so you reach the client and Server Workloads through an Ingress controller instead of directly. The quickstart chart creates the Ingress for you, so you only need to install an ingress controller for it to route traffic.

1. Install the ingress-nginx controller for `kind`:

   ```shell
   kubectl apply -f https://kind.sigs.k8s.io/examples/ingress/deploy-ingress-nginx.yaml
   ```

2. Wait for the Ingress Controller to be ready:

   ```shell
   kubectl wait --namespace ingress-nginx \
     --for=condition=ready pod \
     --selector=app.kubernetes.io/component=controller \
     --timeout=120s
   ```

3. Confirm the Ingresses exist:

   ```shell
   kubectl get ingress -n aembit-quickstart
   ```

   **Expected Output:**

   ```shell
   NAME                       CLASS   HOSTS                                ADDRESS     PORTS   AGE
   aembit-quickstart-client   nginx   client.quickstart.aembit.localhost   localhost   80      30s
   aembit-quickstart-server   nginx   server.quickstart.aembit.localhost   localhost   80      30s
   ```

   You can now reach the workloads at `http://client.quickstart.aembit.localhost` and `http://server.quickstart.aembit.localhost`.

### Verify deployments

[Section titled “Verify deployments”](#verify-deployments)

After deploying the applications, verify that everything is running correctly using the following commands:

1. Check the Helm release status:

   ```shell
   helm status aembit-quickstart -n aembit-quickstart
   ```

   **Expected Output:**

   ```shell
   NAME: aembit-quickstart
   LAST DEPLOYED: Wed Jan 01 10:00:00 2025
   NAMESPACE: aembit-quickstart
   STATUS: deployed
   REVISION: 1
   TEST SUITE: None
   ```

2. List all resources in the namespace:

   ```shell
   kubectl get all -n aembit-quickstart
   ```

   **Expected Output:**

   ```shell
   NAME                                    READY   STATUS    RESTARTS   AGE
   pod/aembit-quickstart-client-abcdef     1/1     Running   0          1m
   pod/aembit-quickstart-server-abcdef     1/1     Running   0          1m
   NAME                               TYPE       CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
   service/aembit-quickstart-client   NodePort   10.109.109.55    <none>        8080:30080/TCP   1m
   service/aembit-quickstart-server   NodePort   10.109.104.236   <none>        9090:30090/TCP   1m
   ```

These outputs help you confirm that you’ve deployed the workloads and services correctly and are functioning as expected.

### Interacting with the applications

[Section titled “Interacting with the applications”](#interacting-with-the-applications)

In this section, you are going to interact with the pre-configured applications. This interaction demonstrates that the Client Workload can connect to the Server Workload but lacks the credentials to authenticate to it.

1. With the client and Server Workloads running, open the [**Client Workload**](http://client.quickstart.aembit.localhost)

2. Click **Get Data**.

   **you’ll receive a failure response** since you haven’t deployed Aembit Edge, nor has Aembit injected the necessary credentials for the Client Workload to access the Server Workload yet.

   ![Failure Message - Client Workload](https://docs.aembit.io/_astro/quickstart_client_workload_unauthorized.C-e1r-h1_Z2mQLrR.webp)

In the next sections, you’ll deploy Aembit Edge. Making it so that Aembit automatically acquires and injects the credential on behalf of the Client Workload so it can then access the Server Workload.

## Deploying Aembit Edge

[Section titled “Deploying Aembit Edge”](#deploying-aembit-edge)

With your workloads deployed, it’s time to integrate Aembit Edge into your system.

Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../concepts/aembit-edge.md) consists of components that customers install within their environment. These components form the core of Aembit’s Workload IAM functionality.

Proceed with deploying Aembit Edge into your environment.

### Create a new Agent Controller

[Section titled “Create a new Agent Controller”](#create-a-new-agent-controller)

The Agent Controller is a helper component that facilitates the registration of other Aembit Edge Components.

1. In your Aembit Tenant, go to **Edge Components** from the left sidebar menu.

2. From the top ribbon menu, select **Deploy Aembit Edge**.

3. Select **Kubernetes** from the list of **Environments**.

   ![Navigate to Deploy Aembit Edge Page](https://docs.aembit.io/_astro/quickstart_navigate_deploy_aembit_edge_page.BTFSt_41_1W3aBH.webp)

4. In the **Prepare Edge Components** section, click to **New Agent Controller**. you’ll see the Agent Controller setup page displayed.

5. Enter a name, such as `Quickstart Agent Controller` (or another user-friendly name).

6. Add an optional description for the controller.

7. For now, ignore the Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../concepts/trust-providers.md) section, as you don’t need it for this quickstart guide.

   ![Create a New Agent Controller](https://docs.aembit.io/_astro/quickstart_create_new_agent_controller.BTnJT9rU_YuWPK.webp)

8. Click **Save**.

   Once saved, your newly created Agent Controller auto-selects from the list of available Agent Controllers.

   This reveals the **Install Aembit Edge Helm Chart** section.

### Deploy the Aembit Edge

[Section titled “Deploy the Aembit Edge”](#deploy-the-aembit-edge)

As part of Aembit Edge, the Agent Proxy is automatically injected within the Client Workload pod. It manages workload identity and securely injects credentials for communication with Server Workloads.

1. In the **Install Aembit Edge Helm Chart** section, make sure that you select the Agent Controller you just created in the dropdown menu.

2. In the **New Agent Controller** section, click **Generate Code** to generate a Device Code.

   The Device Code is a temporary one-time-use code, valid for 15 minutes, that you use during installation to authenticate the Agent Controller with your Tenant.

   Make sure you complete the next steps before the code expires.

   ![Deploy Aembit Edge](https://docs.aembit.io/_astro/quickstart_deploy_aembit_edge.Di403P3s_1Qu8pz.webp)

3. Since you already [installed the Aembit Helm repo](#install-applications), go ahead and install the Aembit Helm chart.

   *From your terminal*, run the following command, making sure to replace:

   * `<tenant>` with your tenant ID (Find this in the Aembit website URL: `<tenant>.aembit.io`)

   * `<deviceCode>` with the code you generated in the Aembit web UI

   ```shell
   helm install aembit aembit/aembit \
     --create-namespace \
     -n aembit \
     --set tenant=<tenant>,agentController.deviceCode=<deviceCode>
   ```

   Tip

   To reduce errors, copy the command from the Aembit Web UI for this step, as it populates your `<tenant>` and `<deviceCode>` for you.

   ![Deploy Aembit Edge Generate Code button](https://docs.aembit.io/_astro/deploy_aembit_edge-generate-code.CDA9UBHb_1uPgM1.webp)

   Aembit Edge is now deployed in your Kubernetes cluster!

4. Check the current state of quickstart Client pod to confirm it is running without the Agent Proxy container.

   The **`READY`** column for the `pod/aembit-quickstart-client-abcdef` should display **`1/1`**, indicating only the Client Workload container is running.

   ```shell
   kubectl get all -n aembit-quickstart
   ```

   **Expected Output:**

   ```shell
   NAME                                    READY   STATUS    RESTARTS   AGE
   pod/aembit-quickstart-client-abcdef     1/1     Running   0          1m
   pod/aembit-quickstart-server-abcdef     1/1     Running   0          1m
   ```

5. Restart the quickstart Client pod to include the Agent Proxy in the deployment:

   ```shell
   kubectl delete pods -l app=aembit-quickstart-client -n aembit-quickstart --grace-period=0 --force
   ```

6. After the pod restarts, verify that the `aembit-quickstart-client` pod now includes two containers: the Client Workload container and the Agent Proxy container.

   After the pod restarts, check its state again. **`READY`** column for the `aembit-quickstart-client` pod should now display **`2/2`**, indicating that both the Client Workload container and the Agent Proxy container are running successfully.

   ```shell
   kubectl get all -n aembit-quickstart
   ```

   **Expected Output:**

   ```shell
   NAME                                    READY   STATUS    RESTARTS   AGE
   pod/aembit-quickstart-client-abcdef     2/2     Running   0          1m
   pod/aembit-quickstart-server-abcdef     1/1     Running   0          1m
   ```

   This step confirms that Aembit has injected Agent Proxy within the Client pod, enabling Aembit to securely manage credentials for communication between Client and Server Workloads.

## Configuring an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../concepts/access-policies.md)

[Section titled “Configuring an ”](#configuring-an-)

Access Policies define the conditions for granting Client Workloads access to Server Workloads. Aembit evaluates access by:

1. Verifying if the Client and Server Workloads match the Access Policy.
2. A Trust Provider authenticates the Client Workload’s identity.
3. The Access Policy meets all Access Conditions.

In this quickstart guide, you have omitted configuring a Trust Provider to simplify your first walkthrough. However, Trust Providers are a critical component in securing all production deployments.

They enable Aembit to authenticate workloads without provisioning long-lived credentials or secrets, making sure that Aembit authenticates and authorizes only workloads it trusts.

Once authorized, Aembit delivers the necessary credentials to Agent Proxy, which it then uses to authenticate the Client workload to the Server Workload.

About Client Workload credentials

Aembit never releases credentials directly to Client Workloads. Instead, Aembit inject credentials into the traffic destined for the target Server Workload, providing secure communication.

1. From your Aembit Tenant, click **Access Policies** in the left sidebar menu.

2. Click **+ New** to open the Access Policy Builder.

   ![Create Access Policy](https://docs.aembit.io/_astro/apb-access-policies-list.B7z6pV7Z_u9Wjn.webp)

   The Access Policy Builder displays component cards in the right panel, with an **Access Policy** status card on the left. The **Access Policy** panel opens by default so you can name and save the policy first.

### Name the Access Policy

[Section titled “Name the Access Policy”](#name-the-access-policy)

Before configuring the policy components, name your Access Policy. You must provide a name before you can save the policy.

1. In the **Name** field, enter `Quickstart Policy` (or another descriptive name).

2. (Optional) Add a description to help identify the policy’s purpose.

   ![Create Access Policy](https://docs.aembit.io/_astro/quickstart_create_access_policy.CO4JHVhq_Zq04f3.webp)

3. Click **Save** to add these details to the policy.

### Configure a Client Workload

[Section titled “Configure a Client Workload”](#configure-a-client-workload)

Client Workloads**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../concepts/client-workloads.md) are software applications that access services provided by Server Workloads. These could be custom apps, CI/CD pipelines, or scripts running without user intervention.

1. In the **Client Workload** card in the right panel, click **+ Configure**.

2. Configure the Client Workload:

   * **Name** - `Quickstart Client` (or another user-friendly name)

   * **Client Identification** - `Kubernetes Pod Name Prefix`

     * **Value** - `aembit-quickstart-client`

3. Click **Save** to add the Client Workload to the policy.

   ![Configuring Client Workload](https://docs.aembit.io/_astro/quickstart_client_workload.K6c7Ky4B_YPHWn.webp)

### Configure a Server Workload

[Section titled “Configure a Server Workload”](#configure-a-server-workload)

[Server Workloads](../../user-guide/access-policies/server-workloads/guides/overview.md) serve requests from Client Workloads and can include APIs, gateways, databases, and more. The configuration settings define the Service Endpoint and Authentication methods, specifying the networking details and Aembit authenticates requests.

1. In the **Server Workload** card in the right panel, click **+ Configure**.

2. Configure the Server Workload:

   * **Name** - `Quickstart Server` (or another user-friendly name)

   * **Host** - `aembit-quickstart-server.aembit-quickstart.svc.cluster.local`

   * **Application Protocol** - `HTTP`

   * **Transport Protocol** - `TCP`

   * **Port** - `9090`

   * **Forward to Port** - `9090`

   * **Authentication Method** - `HTTP Authentication`

   * **Authentication Scheme** - `Bearer`

3. Click **Save** to add the Server Workload to the policy.

   ![Configuring Server Workload](https://docs.aembit.io/_astro/quickstart_server_workload.Bu9fh40T_ZSGuN3.webp)

### Configuring a Credential Provider

[Section titled “Configuring a Credential Provider”](#configuring-a-credential-provider)

Credential Providers**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../concepts/credential-providers.md) supply the access credentials, such as OAuth tokens or API keys, that allow Client Workloads to authenticate with Server Workloads. Aembit can also request and manage tokens from third-party services.

Security Best Practice

In this QuickStart, you are using the API Key option for simplicity. However, Aembit recommends using short-lived credentials whenever possible to enhance security and reduce exposure to risks associated with long-lived credentials.

1. From your web browser, go to the [sandbox Server Workload](http://server.quickstart.aembit.localhost).

2. Click **Generate API Key**.

   This generates a unique API key you’ll use in later in this section.

   Generating more than one key

   Avoid clicking the button multiple times, as only one API key (the last generated) remains active at a time. Copy the API key immediately after creating it, as you need it in the next step.

3. Copy the API key.

   ![Copy API Key - Server Workload](https://docs.aembit.io/_astro/quickstart_server_workload_copy_api_key.DusDE6Es_ZeruHM.webp)

4. In the **Credential Provider** card in the right panel, click **+ Configure**.

5. Configure the Credential Provider:

   * **Name** - `Quickstart API Key` (or another user-friendly name)
   * **Credential Type** - `API Key`
   * **API Key** - Paste the API key you generated from the Server Workload

6. Click **Save** to add the Credential Provider to the policy.

   ![Configuring Credential Provider](https://docs.aembit.io/_astro/quickstart_credential_provider.DPDqdxDE_Z1SgKdQ.webp)

### Finalizing the Access Policy

[Section titled “Finalizing the Access Policy”](#finalizing-the-access-policy)

Once you have configured all components, click **Save Policy & Activate** in the header bar.

## Testing the Access Policy

[Section titled “Testing the Access Policy”](#testing-the-access-policy)

To test your newly configured Access Policy, go to the [sandbox Client Workload](http://client.quickstart.aembit.localhost) and click **Get Data**. Since you activated the Access Policy and Aembit Edge installed the necessary credential into the request, you should see a successful response.

![Success Message - Client Workload](https://docs.aembit.io/_astro/quickstart_client_workload_success.CVp2MEMa_CQiRw.webp)

Congratulations! You’ve created a Access Policy that’s securing access between workloads!

With just a few steps, you have deployed workloads, configured an Access Policy, and successfully authenticated requests, all without the complexity of manual credential management.

This quickstart guide is just the foundation of all the features that Aembit has to offer. It supports powerful capabilities for scaling, securing, and managing workload identity across many environments, providing security and efficiency as your needs grow.

#### Troubleshoot

[Section titled “Troubleshoot”](#troubleshoot)

If you encounter any issues or don’t see a successful response, the Aembit Web UI has a useful **Troubleshooter** that can help you identify potential problems:

1. Go to **Access Policies** and select the Access Policy you created for this quickstart guide.

2. In the **Access Policy** status card, open the **Troubleshooter** tab.

   The **Troubleshooter** tab is available after you save the Access Policy.

   This brings up the Troubleshooter with your Access Policy’s Client and Server Workloads already populated.

   ![Aembit Help Troubleshooter page](https://docs.aembit.io/_astro/quickstart_troubleshooting.C0uHnAao_Z2mPheQ.webp)

3. Inspect and make sure that the **Access Policy Checks**, **Client Workload Checks**, **Credential Provider Checks** and **Server Workload Checks** are **Active** (they have green checks).

   ![Aembit Help Troubleshooter page](https://docs.aembit.io/_astro/quickstart_troubleshooting_sw_checks.CQJxnz87_Babuv.webp)

4. For any sections that aren’t Active, go back to the respective section in the quickstart guide and double check your configurations.

   Also, make sure all the [Prerequisites](#before-you-begin) are complete.

The Troubleshooter helps diagnose potential issues with your configuration. For more details, visit the [Troubleshooter Tool](../../user-guide/troubleshooting/tenant-configuration.md) page.

Still need help? Please [submit a support request](https://aembit.io/support/) to Aembit’s support team.

## What’s next?

[Section titled “What’s next?”](#whats-next)

Now that you’ve completed the basics, it’s time to explore additional features and capabilities to get the most out of Aembit.

See [Quickstart: Add an Access Policy to the core setup](quickstart-access-policy.md) to learn how to:

* **Configure Trust Providers** to enhance workload identity verification and strengthen access control.

* **Set Up Access Conditions** to enforce time-based, geo-based, or custom rules for workload access.

* **Navigate Reporting Tools** to review access events, track policy usage, and analyze workload behavior.

Following the *Quickstart: Access Policy enhancements* page helps you expand beyond the core Aembit setup, guiding you toward features that enhance security, visibility, and scalability.
