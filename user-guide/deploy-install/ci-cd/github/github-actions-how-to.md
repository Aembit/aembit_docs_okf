---
type: how-to
title: "How to retrieve credentials with the Aembit GitHub Action"
description: "Configure the Aembit GitHub Action to retrieve different credential types in your workflows"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/github-actions-how-to/
interface: web-ui
tags: [github, ci-cd, deploy-install]
timestamp: 2026-04-29T13:30:50-04:00
type_inferred: true
---

# How to retrieve credentials with the Aembit GitHub Action

Retrieve credentials from Aembit in your GitHub Actions workflow using the Aembit GitHub Action.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before configuring the action, ensure you have an active Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) linking these components:

* Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md) configured with GitHub OpenID Connect (OIDC) identity
* Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../../get-started/concepts/trust-providers.md) for GitHub Actions
* Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) matching your credential type
* Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) for the target service

## Configure the action

[Section titled “Configure the action”](#configure-the-action)

Add the Aembit GitHub Action to your workflow with the appropriate configuration for your credential type:

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

## Verify it works

[Section titled “Verify it works”](#verify-it-works)

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

2. In your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../../get-started/concepts/administration.md), go to **Reporting** > **Access Authorization Events**.

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

[Section titled “Scaling across workflows”](#scaling-across-workflows)

Use a reusable workflow pattern to standardize credential retrieval across multiple workflows in your repository.

### Reusable workflow pattern

[Section titled “Reusable workflow pattern”](#reusable-workflow-pattern)

Create a reusable workflow that other workflows can call:

.github/workflows/get-aembit-credentials.yml

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
          # Server workload details passed from the calling workflow
          server-host: '${{ inputs.server-host }}'
          server-port: '${{ inputs.server-port }}'
```

Other workflows call it with:

Example workflow calling the reusable workflow

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

[Section titled “Monitoring at scale”](#monitoring-at-scale)

[Log Streams](../../../administration/log-streams/overview.md) aggregate credential access events across all workflows. Use Log Streams for centralized Continuous Integration/Continuous Deployment (CI/CD) monitoring and alerting.

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

### Permission denied

[Section titled “Permission denied”](#permission-denied)

**Symptom:** The action fails with a permission error.

**Cause:** The workflow lacks the required OIDC permissions.

**Solution:** Add the `id-token: write` permission to your workflow:

Required permissions

```yaml
permissions:
  id-token: write
  contents: read
```

### Credential not found

[Section titled “Credential not found”](#credential-not-found)

**Symptom:** The step output is empty when accessed via `${{ steps.aembit.outputs.<name> }}`.

**Cause:** The credential request failed authorization, or you’re using the wrong output name.

**Solution:**

* Verify the `client-id` matches your Trust Provider’s Edge Software Development Kit (SDK) Client ID
* Check that your Access Policy is active
* Confirm the Server Workload host and port match your configuration
* Use the correct output name for your credential type (see [Action output reference](github-actions-reference.md))

### Invalid audience

[Section titled “Invalid audience”](#invalid-audience)

**Symptom:** The action fails with an audience validation error.

**Cause:** Your Trust Provider is in a custom Resource Set, but the action isn’t configured to use it.

**Solution:** Add the `resource-set-id` input — see [Configure the action](#configure-the-action).

### Credential format mismatch

[Section titled “Credential format mismatch”](#credential-format-mismatch)

**Symptom:** The credential works but isn’t in the expected format.

**Cause:** The Credential Provider type doesn’t match how you’re using the credential.

**Solution:** Verify your Credential Provider type matches your usage. Each credential type provides different step outputs:

* API Key: `api-key`
* Username/Password: `username` and `password`
* OAuth: `token`

See the [Action output reference](github-actions-reference.md) for the complete list.

## Related

[Section titled “Related”](#related)

* [Tutorial](github-actions-tutorial.md) - Step-by-step first setup
* [Reference](github-actions-reference.md) - All action parameters
* [GitHub Trust Provider](../../../access-policies/trust-providers/github-trust-provider.md) - Trust Provider configuration
* [Access Authorization Events](../../../audit-report/access-authorization-events.md) - Viewing credential request logs
