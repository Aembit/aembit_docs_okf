---
type: how-to
title: "How to retrieve credentials with the Aembit GitHub Action"
description: "Configure the Aembit GitHub Action to retrieve different credential types in your workflows"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/github-actions-how-to/
interface: web-ui
tags: ["github", "ci-cd", "deploy-install"]
timestamp: 2026-09-22T15:17:29-07:00
---

# How to retrieve credentials with the Aembit GitHub Action

Retrieve credentials from Aembit in your GitHub Actions workflow using the Aembit GitHub Action.

## Prerequisites

Before configuring the action, ensure you have an active Access Policy linking these components:

* Client Workload configured with GitHub OpenID Connect (OIDC) identity
* Trust Provider for GitHub Actions
* Credential Provider matching your credential type
* Server Workload for the target service

## Configure the action

Add the Aembit GitHub Action to your workflow with the appropriate configuration for your credential type. The `credential-type` input names the Credential Provider type in your Access Policy and decides which step outputs the action sets.

Optionally, add the `resource-set-id` input if your Trust Provider lives in a custom [Resource Set](../../../administration/resource-sets/overview.md).

* API Key

  ```yaml
  permissions:
    id-token: write
    contents: read


  jobs:
    call-api:
      runs-on: ubuntu-latest
      steps:
        - name: Get API Key from Aembit
          id: aembit
          uses: Aembit/get-credentials@v1
          with:
            client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
            credential-type: 'ApiKey'
            server-host: 'api.example.com'
            server-port: '443'


        - name: Use the credential
          env:
            API_KEY: ${{ steps.aembit.outputs.api-key }}
          run: |
            curl -H "X-API-Key: $API_KEY" \
              https://api.example.com/endpoint
  ```

  The action provides the API key as the `api-key` [step output](github-actions-reference.md).

* OAuth Token

  ```yaml
  permissions:
    id-token: write
    contents: read


  jobs:
    call-api:
      runs-on: ubuntu-latest
      steps:
        - name: Get OAuth Token from Aembit
          id: aembit
          uses: Aembit/get-credentials@v1
          with:
            client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
            credential-type: 'OAuthToken'
            server-host: 'oauth.example.com'
            server-port: '443'


        - name: Use the credential
          env:
            TOKEN: ${{ steps.aembit.outputs.token }}
          run: |
            curl -H "Authorization: Bearer $TOKEN" \
              https://api.example.com/endpoint
  ```

  For Open Authorization (OAuth) credentials, Aembit handles the token exchange. The action provides the access token as the `token` [step output](github-actions-reference.md).

* Username/Password

  ```yaml
  permissions:
    id-token: write
    contents: read


  jobs:
    call-api:
      runs-on: ubuntu-latest
      steps:
        - name: Get credentials from Aembit
          id: aembit
          uses: Aembit/get-credentials@v1
          with:
            client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
            credential-type: 'UsernamePassword'
            server-host: 'service.example.com'
            server-port: '443'


        - name: Use the credentials
          env:
            USERNAME: ${{ steps.aembit.outputs.username }}
            PASSWORD: ${{ steps.aembit.outputs.password }}
          run: |
            curl -u "$USERNAME:$PASSWORD" \
              https://service.example.com/endpoint
  ```

  The action provides username/password credentials as the `username` and `password` [step outputs](github-actions-reference.md).

* AWS STS Federation

  ```yaml
  permissions:
    id-token: write
    contents: read


  jobs:
    call-aws:
      runs-on: ubuntu-latest
      steps:
        - name: Get AWS credentials from Aembit
          id: aembit
          uses: Aembit/get-credentials@v1
          with:
            client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
            credential-type: 'AwsStsFederation'
            server-host: 's3.amazonaws.com'
            server-port: '443'


        - name: Configure AWS credentials
          uses: aws-actions/configure-aws-credentials@v4
          with:
            aws-access-key-id: ${{ steps.aembit.outputs.aws-access-key-id }}
            aws-secret-access-key: ${{ steps.aembit.outputs.aws-secret-access-key }}
            aws-session-token: ${{ steps.aembit.outputs.aws-session-token }}
            aws-region: us-east-1


        - name: Use the credentials
          run: aws sts get-caller-identity
  ```

  The action provides temporary AWS credentials as the `aws-access-key-id`, `aws-secret-access-key`, and `aws-session-token` [step outputs](github-actions-reference.md). Pass them to `aws-actions/configure-aws-credentials` so that later steps in the job use them through the AWS CLI and AWS SDKs.

  #### Select among multiple AWS STS Federation Credential Providers

  An Access Policy can hold multiple AWS STS Federation Credential Providers, each with its own **Access Key ID selector**. For how Aembit uses the selector, see [Using multiple AWS STS Federation Credential Providers](../../../access-policies/credential-providers/aws-security-token-service-multiple.md). To choose one, add the `aws-access-key-id` input with the selector of the Credential Provider you want. The input requires version 1.3.0 or later of the action:

  ```yaml
        - name: Get AWS credentials from Aembit
          id: aembit
          uses: Aembit/get-credentials@v1.3.0
          with:
            client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
            credential-type: 'AwsStsFederation'
            server-host: 's3.amazonaws.com'
            server-port: '443'
            aws-access-key-id: 'AKIADUMMYFORROLEA'
  ```

  Selector values contain uppercase letters and numbers only, up to 256 characters, and must match the value saved in the Credential Provider’s mapping exactly. Omit the input when the Access Policy has a single AWS STS Federation Credential Provider. A selector that matches no Credential Provider in the Access Policy fails the step. Omitting the input when the Access Policy holds more than one also fails the step, because Aembit returns no credential rather than choosing for you.

## Verify it works

After running your workflow:

1. Check the GitHub Actions logs for successful credential retrieval. A successful run shows output similar to:

   ```text
   Run Aembit/get-credentials@v1
   Requesting credentials from Aembit...
   ✓ Successfully authenticated with Aembit
   ✓ Credential retrieved for server workload: api.example.com:443
   ✓ Credential available as step output
   ✓ Credential masked in logs
   ```

2. In your Aembit Tenant, go to **Reporting** > **Access Authorization Events**.

3. Look for events matching your Client Workload. Verify the status shows **Authorized**.

If the action fails, you’ll see error output like:

```text
Run Aembit/get-credentials@v1
Requesting credentials from Aembit...
✗ Authorization failed: Access Policy not matched
  Client ID: abc123...
  Server Workload: api.example.com:443
Error: Unable to retrieve credentials. Check your Access Policy configuration.
```

## Scaling across workflows

Use a reusable workflow pattern to standardize credential retrieval across multiple workflows in your repository.

### Reusable workflow pattern

Create a reusable workflow that other workflows can call:

**.github/workflows/get-aembit-credentials.yml**

```yaml
name: Get Aembit Credentials


# Allow other workflows to call this workflow
on:
  workflow_call:
    inputs:
      server-host:
        description: 'Hostname of the target server workload'
        required: true
        type: string
      server-port:
        description: 'Port of the target server workload'
        required: false
        type: string
        default: '443'
    # Define outputs that calling workflows can access
    outputs:
      token:
        description: 'The retrieved credential'
        value: ${{ jobs.get-creds.outputs.token }}


jobs:
  get-creds:
    runs-on: ubuntu-latest
    # Pass the token output to the workflow output
    outputs:
      token: ${{ steps.aembit.outputs.token }}
    # Required for GitHub to issue OIDC tokens
    permissions:
      id-token: write
    steps:
      - name: Get credentials
        id: aembit
        uses: Aembit/get-credentials@v1
        with:
          # Client ID from your Trust Provider (store as repository secret)
          client-id: '${{ secrets.AEMBIT_CLIENT_ID }}'
          credential-type: 'OAuthToken'
          # Server workload details passed from the calling workflow
          server-host: '${{ inputs.server-host }}'
          server-port: '${{ inputs.server-port }}'
```

Other workflows call it with:

**Example workflow calling the reusable workflow**

```yaml
jobs:
  my-job:
    # Reference the reusable workflow file
    uses: ./.github/workflows/get-aembit-credentials.yml
    with:
      server-host: 'api.example.com'
    # Pass secrets to the reusable workflow
    secrets: inherit
```

### Monitoring at scale

[Log Streams](../../../administration/log-streams/overview.md) aggregate credential access events across all workflows. Use Log Streams for centralized Continuous Integration/Continuous Deployment (CI/CD) monitoring and alerting.

## Troubleshooting

### Permission denied

**Symptom:** The action fails with a permission error.

**Cause:** The workflow lacks the required OIDC permissions.

**Solution:** Add the `id-token: write` permission to your workflow:

**Required permissions**

```yaml
permissions:
  id-token: write
  contents: read
```

### Credential not found

**Symptom:** The step output is empty when accessed via `${{ steps.aembit.outputs.<name> }}`.

**Cause:** The credential request failed authorization, or you’re using the wrong output name.

**Solution:**

* Verify the `client-id` matches your Trust Provider’s Edge Software Development Kit (SDK) Client ID
* Check that your Access Policy is active
* Confirm the Server Workload host and port match your configuration
* Use the correct output name for your credential type (see [Action output reference](github-actions-reference.md))

### Invalid audience

**Symptom:** The action fails with an audience validation error.

**Cause:** Your Trust Provider is in a custom Resource Set, but the action isn’t configured to use it.

**Solution:** Add the `resource-set-id` input. See [Configure the action](#configure-the-action).

### Wrong AWS role assumed

**Symptom:** The step succeeds, but `aws sts get-caller-identity` reports an IAM role you didn’t expect. Or, the step fails when the Access Policy has more than one AWS STS Federation Credential Provider.

**Cause:** The `aws-access-key-id` input is missing, or its value doesn’t match the **Access Key ID selector** of the Credential Provider you want.

**Solution:** Set `aws-access-key-id` to the **AWS Access Key Id** value saved in that Credential Provider’s mapping in the Access Policy Builder, exactly as saved, and pin `Aembit/get-credentials@v1.3.0` or later. See [Select among multiple AWS STS Federation Credential Providers](#select-among-multiple-aws-sts-credential-providers).

### Credential format mismatch

**Symptom:** The credential works but isn’t in the expected format.

**Cause:** The Credential Provider type doesn’t match how you’re using the credential.

**Solution:** Verify your Credential Provider type matches your usage. Each credential type provides different step outputs:

* API Key: `api-key`
* Username/Password: `username` and `password`
* OAuth: `token`
* AWS STS Federation: `aws-access-key-id`, `aws-secret-access-key`, and `aws-session-token`

See the [Action output reference](github-actions-reference.md) for the complete list.

## Related

* [Tutorial](github-actions-tutorial.md) - Step-by-step first setup
* [Reference](github-actions-reference.md) - All action parameters
* [GitHub Trust Provider](../../../access-policies/trust-providers/github-trust-provider.md) - Trust Provider configuration
* [Access Authorization Events](../../../audit-report/access-authorization-events.md) - Viewing credential request logs
