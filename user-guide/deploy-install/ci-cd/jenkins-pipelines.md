---
type: how-to
title: "Injecting credentials into Jenkins Pipelines with Aembit"
description: "Set up Jenkins to use Aembit's OIDC ID Token Trust Provider for secure CI/CD authentication without static credentials"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/jenkins-pipelines/
interface: web-ui
tags: ["ci-cd", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Injecting credentials into Jenkins Pipelines with Aembit

Aembit provides many different deployment options you can use to deploy Aembit Edge Components in your environment. Each of these options provides similar features and functionality; however, the steps for each of these options are specific to the deployment option you select.

This page describes the process to use the [Aembit CLI](../../../dev-guide/cli/overview.md) in [Jenkins Pipelines](https://www.jenkins.io/doc/book/pipeline/) (recommended) and Freestyle projects, providing step-by-step instructions for each approach.

Configure Jenkins to authenticate with Aembit using OpenID Connect (OIDC) tokens, enabling secure access to your infrastructure without managing static credentials in your CI/CD pipelines.

This configuration allows Jenkins jobs to obtain temporary credentials from Aembit using OIDC tokens that Jenkins issues eliminating the need to store long-lived secrets in Jenkins.

## Prerequisites

Before you begin, ensure you have:

* Jenkins instance with administrator access
* An Aembit Tenant with read and write permissions for Trust Providers and Access Policies
* Basic familiarity with Jenkins job configuration and pipeline scripting

## What you’ll do

This page walks you through the following tasks:

* [Install the Jenkins OIDC plugin](#install-the-jenkins-oidc-plugin)
* [Configure Jenkins system settings](#configure-jenkins-system-settings)
* [Create OIDC credentials in Jenkins](#create-oidc-credentials-in-jenkins)
* [Set up Aembit OIDC ID Token Trust Provider](#set-up-aembit-oidc-id-token-trust-provider)
* [Configure an Access Policy](#configure-an-access-policy)
* [Create a test Jenkins job](#create-a-test-jenkins-job)
* [Troubleshooting common issues](#troubleshooting-common-issues)

## Install the Jenkins OIDC plugin

This procedure requires a third-party plugin to issue OIDC tokens.

You’ll install the **OpenID Connect Provider Plugin**, which enables Jenkins to act as an OIDC provider, issuing tokens that your jobs can use to authenticate with Aembit.

1. Go to your Jenkins instance and log in as an administrator.

2. In the Jenkins UI, go to **Manage Jenkins -> Plugins**.

3. Select the **Available plugins** tab.

4. Search for “OIDC Provider” and install the **OpenID Connect Provider Plugin** from this URL: `https://plugins.jenkins.io/oidc-provider/`.

   > **OIDC plugin selection**
   >
   > Multiple OIDC-related plugins exist. **Ensure you install the “OpenID Connect Provider Plugin” specifically**, which enables Jenkins to issue its own OIDC tokens. Note that this plugin don’t appear in the “Installed” plugins list after installation. The other common plugin, “OpenID Connect authentication,” typically handles Single Sign-On (SSO) *into* Jenkins and doesn’t issue tokens.

5. Restart Jenkins when prompted to complete the installation.

## Configure Jenkins system settings

**The Jenkins URL configuration is critical for OIDC token verification**.

> **Choosing your attestation method**
>
> The Jenkins URL you configure here determines which attestation method you’ll use when setting up the Aembit Trust Provider later:
>
> * **OIDC Discovery** (recommended): Your Jenkins URL must be publicly accessible to Aembit, have a valid TLS certificate, and allow access to `/oidc/.well-known/jwks.json`
> * **Upload JWKS**: Use this if your Jenkins instance uses self-signed certificates, has network restrictions, or returns `localhost:8080` URLs in OIDC configuration (common in Docker/proxy deployments)

1. In the Jenkins UI, go to **Manage Jenkins -> System**.
2. Locate the **Jenkins Location** section.
3. Set the **Jenkins URL** to your actual domain name (for example, `https://jenkins.my-company.com/`).
4. Click **Save** to apply the changes.

## Create OIDC credentials in Jenkins

Jenkins credentials store the configuration needed to issue OIDC tokens for your jobs.

1. In the Jenkins UI, go to **Manage Jenkins -> Credentials**.

2. Click on the **Global** domain (or create a new domain if needed).

   > **Tip**
   >
   > Use the Global scope for credentials that need to be available to all jobs. Jenkins reserves System scope for internal operations like agent connections.

3. Click **Add Credentials** to create a new credential.

4. In the credential creation form, configure the following:

   * From the **Kind** dropdown, select **OpenID Connect id token**
   * **Scope** - Select **Global** to make the credential available to all jobs.
   * **Issuer** - **Leave this field blank to use Jenkins as the token issuer** (`https://jenkins.aembit.io/oidc`) The Jenkins OIDC Provider plugin automatically uses the **Jenkins URL** configured in system settings as the issuer.
   * **Audience** - Enter a custom identifier for your use case (for example, `aembit-jenkins-prod`).
   * **ID** - Leave this field blank to let Jenkins generate a unique ID. Jenkins uses this ID internally.
   * **Description** - Enter a descriptive name (this serves as the credential’s display name). This is important for the credential to appear in job dropdowns.

   > **Caution**
   >
   > If you fill in the **Default Issuer URI** field, Jenkins won’t include this credential in its public key manifest, causing token verification to fail. **Leave this field blank** unless you’re using an external OIDC provider.

5. Click **OK** to save the credential.

   Jenkins displays your new credential in the credentials list and you can reference it in Jenkins jobs.

6. Record the **ID** of the credential you just created. You’ll use this ID in your Jenkins job configuration to reference the OIDC credential.

   > **Note**
   >
   > The ID is a unique identifier for the credential, which you’ll use in your Jenkins job configurations. You can find this ID in the credentials list or by clicking on the credential to view its details.

## Set up Aembit OIDC ID Token Trust Provider

Configure Aembit to trust tokens issued by your Jenkins instance:

1. Log in to your Aembit Tenant.

2. Go to **Trust Providers** in the left sidebar.

3. Click **+ New**. This displays the Trust Provider form.

4. Give the Trust Provider a **Name** and optional **Description**.

5. Select **OIDC ID Token** as the **TRUST PROVIDER**.

6. Configure the following **Match Rules** to validate incoming tokens by clicking **+ New Rule**:

   * **`aud`** - Enter the **Audience** value you configured in the Jenkins credential.
   * **`iss`** - Enter the **Issuer** value, which should match your Jenkins base URL, such as: `https://jenkins.my-company.com/oidc`.
   * **`sub`** - Optionally specify patterns to match specific jobs or leave blank to accept any subject. The subject claim in Jenkins OIDC tokens typically contains job information, allowing for fine-grained access control.

7. Select an **Attestation Method** based on your Jenkins environment:

   * OIDC Discovery

     Use OIDC Discovery when Jenkins has a valid TLS certificate and is publicly accessible. This is the recommended method for standard deployments.

     > **Self-signed certificates**
     >
     > If your Jenkins instance uses self-signed certificates, OIDC Discovery fails. Use the **Upload JWKS** attestation method instead.

     1. Select **OIDC Discovery** as the attestation method.
     2. In the **OIDC Endpoint** field that appears, enter your Jenkins issuer URL (for example, `https://jenkins.my-company.com/oidc`).\
        Ensure this matches the issuer URL configured in Jenkins. Aembit automatically discovers the OIDC configuration from this endpoint.

   * JWKS

     Use **Upload JWKS** for secure environments where Jenkins shouldn’t be publicly accessible, or when network policies prevent OIDC Discovery. This method provides manual control over key distribution and works well for air-gapped or isolated deployments.

     1. Select **Upload JWKS** as the attestation method.

        To fill in the **JWKS Content** field, you need to retrieve the JWKS (JSON Web Key Set) from your Jenkins instance, by continuing with these steps:

     2. Log into your Jenkins instance as an administrator.

     3. Discover your Jenkins JWKS endpoint using the Jenkins Script Console:\
        From your Jenkins instance, go to **Manage Jenkins -> Script Console**. Run this script to find your `jwks_uri`:

        ```groovy
        def url = new URL("http://localhost:8080/oidc/.well-known/openid-configuration")
        def connection = url.openConnection()
        def response = connection.inputStream.text
        def json = new groovy.json.JsonSlurper().parseText(response)
        println "JWKS URI: ${json.jwks_uri}"
        ```

        Record the URI shown (typically `http://localhost:8080/oidc/jwks`).

     4. Retrieve the JWKS content using the URI from the previous step:\
        In the same Script Console, run this script (replace the URL with your JWKS URI from step 2):

        ```groovy
        def url = new URL("<your-jwks-uri>")  // Use the URI from step 2
        def connection = url.openConnection()
        def response = connection.inputStream.text
        println "JWKS Content:"
        println "// Copy only the following JSON\n"
        println response
        ```

        Copy the entire JSON object from the console (copy the brackets and all content between them).

     5. Return to your Aembit Tenant.

     6. In the **JWKS Content** field that appears in Aembit, paste the JSON content.

   > **Other attestation methods**
   >
   > The **Public Key** and **Symmetric Key** attestation methods aren’t applicable for the OpenID Connect Provider Plugin because:
   >
   > * OpenID Connect Provider Plugin uses asymmetric cryptography internally and doesn’t export keys in PEM/CER format
   > * OpenID Connect Provider Plugin doesn’t support symmetric key signing configuration
   > * Keys are only available in JWKS format through the OpenID Connect Provider Plugin’s standard OIDC endpoints

8. Click **Save** to create the Trust Provider.

   Aembit displays your new Trust Provider in the list, showing its ID and other details.

9. After saving, select your new Trust Provider to view its details.

10. Locate and copy the **Edge SDK Client ID**.

    This Edge SDK Client ID is what you’ll use as the `--client-id` parameter in your Jenkins pipeline code. For detailed steps on finding this ID, see [Find your Edge SDK Client ID](../../access-policies/trust-providers/get-edge-sdk-client-id.md).

## Configure an Access Policy

From your Aembit Tenant, create a new Access Policy or update an existing one to start using the Trust Provider you just created.

1. Go to **Access Policies** and select an existing policy to open the Access Policy Builder, or click **+ New** to create a new one.
2. In the **Trust Provider** card in the right panel, click **+ Configure**, then select the Jenkins OIDC ID Token Trust Provider you created.
3. Configure the remaining policy components (Client Workload, Server Workload, Credential Provider) as needed.
4. Click **Save Policy & Activate**.

## Create a test Jenkins job

Return to the Jenkins UI to verify your configuration by creating a sample job that uses the OIDC credential.

This job injects the OIDC token into a Jenkins job environment variable for use by the Aembit CLI.

* Pipeline

  > **Prerequisites**
  >
  > Pipeline jobs require the **[Pipeline plugin](https://plugins.jenkins.io/workflow-aggregator/)**, which is typically included in modern Jenkins installations. If you don’t see the Pipeline option when creating a new item, install the Pipeline plugin from **Manage Jenkins -> Plugins**.

  1. In Jenkins, click **New Item**.

  2. Enter a job name and select **Pipeline**.

  3. Click **OK** to create the job.

  4. In the job configuration page, scroll to the **Pipeline** section.

  5. Select **Pipeline script** as the **Definition**.

  6. In the **Script** text area, paste the following pipeline code, making sure to replace the placeholder values with your actual Aembit configuration values:

     ```groovy
     pipeline {
         agent any


         environment {
             // Set Aembit Agent CLI version - check https://releases.aembit.io/agent/ for latest version
             AEMBIT_AGENT_VERSION = '1.24.3328'


             // Replace with your actual values from Aembit
             EDGE_SDK_CLIENT_ID = '<yourEdgeSdkClientId>'
             SERVER_WORKLOAD_HOST = '<yourServerWorkloadHost>'
             SERVER_WORKLOAD_PORT = '<yourServerWorkloadPort>'
         }


         stages {
             stage('Download Aembit CLI') {
                 steps {
                     script {
                         // Download and extract Aembit Agent CLI
                         sh '''
                             echo "Downloading Aembit Agent CLI version ${AEMBIT_AGENT_VERSION}..."
                             curl -O "https://releases.aembit.io/agent/${AEMBIT_AGENT_VERSION}/linux/amd64/aembit_agent_cli_linux_amd64_${AEMBIT_AGENT_VERSION}.tar.gz"


                             echo "Extracting CLI..."
                             tar xzf "aembit_agent_cli_linux_amd64_${AEMBIT_AGENT_VERSION}.tar.gz"


                             echo "Making CLI executable..."
                             chmod +x "aembit"


                             echo "CLI download complete"
                         '''
                     }
                 }
             }


             stage('Get Aembit Credentials') {
                 steps {
                     withCredentials([string(credentialsId: '<your-oidc-credential-id>', variable: 'OIDC_TOKEN')]) {
                         script {
                             sh '''
                                 echo "Retrieving credentials from Aembit..."


                                 # Capture the Aembit CLI output and evaluate it
                                 AEMBIT_OUTPUT=$(./aembit credentials get \
                                     --client-id "${EDGE_SDK_CLIENT_ID}" \
                                     --server-workload-host "${SERVER_WORKLOAD_HOST}" \
                                     --server-workload-port "${SERVER_WORKLOAD_PORT}" \
                                     --log-level=debug \
                                     --id-token "${OIDC_TOKEN}")


                                 echo "Aembit CLI output:"
                                 echo "$AEMBIT_OUTPUT"


                                  # Evaluate the export commands to set environment variables
                                 eval "$AEMBIT_OUTPUT"


                                 # Write credentials to a file that can be sourced in later stages
                                 echo "$AEMBIT_OUTPUT" > aembit_credentials.env


                                 echo "Successfully retrieved credentials from Aembit"
                             '''
                         }
                     }
                 }
             }


             stage('Use Retrieved Credentials') {
                 steps {
                     script {
                         sh '''#!/bin/bash
                             # Source the credentials from the previous stage
                             source aembit_credentials.env


                             echo "Using retrieved credentials for authenticated operations..."
                             echo "Available credential variables:"
                             env | grep -E '^(TOKEN|ACCESS_TOKEN|API_KEY)' || echo "No standard credential variables found"


                             # Example: Use with curl
                             # curl -H "Authorization: Bearer $TOKEN" https://api.example.com/data


                             # Example: Use with your application
                             # ./your-application --token="$TOKEN"


                             echo "Authenticated operations completed successfully"
                         '''
                     }
                 }
             }
         }


         post {
             always {
                 // Clean up downloaded files
                 sh '''
                     echo "Cleaning up downloaded files..."
                     rm -f aembit_agent_cli_linux_amd64_*.tar.gz
                     rm -rf aembit_agent_cli_linux_amd64_*
                 '''
             }
             success {
                 echo 'Pipeline completed successfully!'
             }
             failure {
                 echo 'Pipeline failed. Check the logs for details.'
             }
         }
     }
     ```

     > **Caution**
     >
     > Replace `'your-oidc-credential-id'` with the actual ID of the OIDC credential you created earlier. You can find this ID in the Jenkins credentials page.

     > **File-based credential sharing**
     >
     > This pipeline uses a file-based approach (`aembit_credentials.env`) to share dynamic credentials between stages.
     >
     > In Jenkins Pipelines, environment variables set in one stage don’t automatically carry over to other stages because each stage runs in a separate shell session. Writing credentials to a file and sourcing it in subsequent stages is the most reliable method for sharing dynamic credentials between pipeline stages, and it’s a best practice.

  7. Click **Save**.

     Jenkins takes you to the job’s main page, where you can see the configuration summary.

* Freestyle Project

  1. In Jenkins, click **New Item**.

  2. Enter a job name and select **Freestyle project**.

  3. Click **OK** to create the job.

  4. In the job configuration page, scroll to the **Build Environment** section.

  5. Check **Use secret text or file**.

  6. Click **Add -> Secret text**.

  7. Configure the secret text binding:

  8. In the **Environment** section, check `Use secret text(s) or file(s)`, and enter the following:

     * **Variable** - Enter `OIDC_TOKEN` (or your preferred environment variable name).
     * **Credentials**: Select **Specific credentials** and choose the OIDC credential you created earlier. This sets up the OIDC discovery URL that Aembit CLI uses.

  9. In the **Build Steps** section, click **Add build step**, and select the appropriate execution method:

     * **Execute shell** (Linux/macOS)
     * **Execute Windows batch command** (Windows)

  10. Add commands to download and use the Aembit CLI:

      ```shell
      #!/bin/bash
      # Set Aembit Agent CLI version - check https://releases.aembit.io/agent/ for latest version
      AEMBIT_AGENT_VERSION=1.24.3328
      # Download Aembit Agent CLI from official releases
      curl -O "https://releases.aembit.io/agent/$AEMBIT_AGENT_VERSION/linux/amd64/aembit_agent_cli_linux_amd64_$AEMBIT_AGENT_VERSION.tar.gz"
      tar xzf "aembit_agent_cli_linux_amd64_$AEMBIT_AGENT_VERSION.tar.gz"


      # Base64 encode the OIDC token for debugging (optional)
      echo $OIDC_TOKEN | base64


      # Use the OIDC token to get credentials from Aembit
      ./aembit credentials get \
        --client-id aembit:qa:bc74ee:identity:oidc_id_token:468ffc01-4306-48ad-97cc-7a4e1a10c945 \
        --server-workload-host graph.microsoft.com \
        --server-workload-port 443 \
        --log-level=info \
        --id-token $OIDC_TOKEN


      # Example: Use the retrieved credentials
      echo "Successfully retrieved credentials"
      # Your application logic would use these credentials here
      # For example, making an authenticated API call
      ```

  11. Save the job configuration.

      Jenkins takes you to the job’s main page, where you can see the configuration summary.

### Run and verify the job

Now that you’ve configured the Jenkins job, you can run it to verify that it retrieves credentials from Aembit using the OIDC token.

To run and verify the job, follow these steps:

1. On the job’s main page, click **Build Now** to trigger a build.

2. Wait for the build to complete. You can monitor the build progress in the **Build History** section.

3. Click on the build number to view the build details.

4. In the build details, click **Console Output** to see the job’s execution log.

   The console output should show the following:

   * The Aembit Agent CLI downloading successfully
   * The OIDC token Jenkins retrieved and used
   * The Aembit CLI successfully authenticating using the OIDC token
   * Credentials Jenkins retrieved as expected

   If you see these messages, your Jenkins job is correctly configured to use Aembit OIDC authentication. The output should look similar to the following example console output:

   ```shell
   Started by user Jenkins Admin
   Running as SYSTEM
   Agent default-zdbrp is provisioned from template default
   ---
   ...
   ... omitted for brevity ...
   ...
   2025-07-29T00:03:03.912925Z INFO aembit_assessment::gather Detected AWS platform.
   2025-07-29T00:03:03.913054Z INFO aembit_assessment::gather Getting AWS EC2 metadata.
   2025-07-29T00:03:03.913058Z INFO aembit_assessment::aws Getting AWS EC2 assessment.
   2025-07-29T00:03:04.210428Z INFO aembit_controlplane::commands Received credentials for access policy. Context ID: c8fe6a20-cb6c-4bd6-b1eb-2cdd9b87bd76.
   export TOKEN=my_secure_api_key_abc123xyz789
   Successfully retrieved credentials
   Finished: SUCCESS
   ```

   You’ll know the job is successful if you see the `export TOKEN=<your_secret_here>` line in the output, indicating that the Aembit CLI successfully retrieved credentials using the OIDC token.

## Troubleshooting common issues

### Token verification failures

**Problem** - Aembit reports that it can’t verify the OIDC token.

**Possible causes and solutions**:

* **Jenkins URL is localhost** - Update your Jenkins base URL to use a publicly accessible domain name. Even when the Jenkins instance is public, the plugin’s JWKS endpoint might initially reference `localhost:8080`, which you must replace with your actual public URL so Aembit can reach it.
* **Default Issuer URI has content** - Leave the Default Issuer URI blank in the Jenkins credential configuration.
* **Discovery endpoint unreachable** - Verify that `https://your-jenkins-url/oidc/.well-known/openid_configuration` provides access from the internet.

### Missing public keys

**Problem** - Aembit can’t retrieve public keys to verify tokens.

**Solution** - Use the Jenkins Script Console to verify and retrieve the JWKS:

1. First, check what JWKS URI Jenkins is actually serving:

   ```groovy
   def url = new URL("http://localhost:8080/oidc/.well-known/openid-configuration")
   def connection = url.openConnection()
   def response = connection.inputStream.text
   def json = new groovy.json.JsonSlurper().parseText(response)
   println "JWKS URI: ${json.jwks_uri}"
   ```

2. If the URI shows `localhost:8080`, this confirms the issue. Retrieve the JWKS content directly:

   ```groovy
   def url = new URL("http://localhost:8080/oidc/jwks")  // Use the URI from step 1
   def connection = url.openConnection()
   def response = connection.inputStream.text
   println "JWKS Content:"
   println response
   ```

3. Use the **Upload JWKS** attestation method in Aembit and paste the JSON output from step 2.

The JWKS endpoint should return a JSON object containing the public keys used to sign OIDC tokens.

### Jenkins returns localhost URLs in OIDC configuration

**Problem** - Jenkins OIDC endpoints reference `localhost:8080` instead of your public domain, causing Aembit token verification to fail even when you configure the Jenkins URL correctly.

**This commonly occurs when**:

* Jenkins runs behind a reverse proxy without proper header forwarding
* Jenkins runs in containers with incorrect hostname resolution
* Startup scripts in `JENKINS_HOME/init.groovy.d/` override the Jenkins URL setting

**Solutions**:

1. **Use Upload JWKS method (recommended quick fix)**: Switch your Aembit Trust Provider to use **Upload JWKS** attestation instead of OIDC Discovery.\
   This bypasses the localhost URL issue entirely.

2. **Fix reverse proxy configuration**: Ensure your reverse proxy forwards these headers to Jenkins:

   ```plaintext
   X-Forwarded-For
   X-Forwarded-Proto
   X-Forwarded-Host
   ```

3. **Check for URL override scripts**: Look for Groovy scripts in `JENKINS_HOME/init.groovy.d/` that might reset the Jenkins URL to localhost after restart.

4. **Set environment variables for containers**: For containerized Jenkins, explicitly set `JENKINS_URL` environment variable:

   ```shell
   JENKINS_URL=https://jenkins.your-company.com
   ```

5. **Verify the actual OIDC configuration**: Use Jenkins Script Console to check what URLs Jenkins is serving:

   ```groovy
   def url = new URL("http://localhost:8080/oidc/.well-known/openid-configuration")
   def connection = url.openConnection()
   def response = connection.inputStream.text
   def json = new groovy.json.JsonSlurper().parseText(response)
   println "Issuer: ${json.issuer}"
   println "JWKS URI: ${json.jwks_uri}"
   ```

### Credential not appearing in dropdown

**This only applies to Jenkins Freestyle projects.**

**Problem** - Your OIDC credential doesn’t appear in the job’s credential selection dropdown.

**Solution** - Ensure you provided a **Description** when creating the credential. Jenkins uses the description as the display name, and credentials without descriptions may not appear in selection lists.

## Deployment considerations

### Standard Jenkins installations

For traditional Jenkins installations, apply the configuration steps listed earlier directly.

Ensure your Jenkins instance provides access from the internet for OIDC discovery to work correctly.

### Generic nature of the OpenID Connect Provider plugin

The Jenkins OIDC Provider plugin operates generically. The resulting JSON Web Token (JWT) functions consistently **whether you deploy Jenkins as a VM or otherwise**, or if the token originates from other providers like GitHub or GitLab. This highlights the broad applicability of the generated tokens and the standard implementation of the plugin.

## Understanding terminology

The integration involves multiple identifiers that serve different purposes:

* **OIDC Client ID** - The identifier for the Jenkins credential (configured in the credential’s audience field)
* Client Workload ID - The value you pass to `--client-workload-id`. Supply the Client Workload’s Aembit Client ID, not the workload’s own resource ID.
* Edge SDK Client ID - The identifier from your OIDC Trust Provider in your Aembit Tenant (passed to `--client-id`)

These are distinct values that serve different parts of the authentication flow.

## Next steps

With Jenkins and Aembit OIDC integration configured, you can:

* [Set up additional CI/CD integrations](overview.md)
* [Configure multiple credential providers for complex workflows](../../access-policies/credential-providers/multiple-credential-providers.md)
* [Implement access conditions for enhanced security](../../access-policies/access-conditions/overview.md)
