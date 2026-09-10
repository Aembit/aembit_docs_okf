---
type: reference
title: "Aembit with GitHub Actions"
description: "Securely deliver credentials to GitHub Actions workflows without storing secrets in GitHub"
resource: https://docs.aembit.io/user-guide/deploy-install/ci-cd/github/
interface: web-ui
tags: ["github", "ci-cd", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Aembit with GitHub Actions

Aembit enables your GitHub Actions workflows to retrieve credentials at runtime instead of storing secrets in GitHub. This eliminates the risk of secret sprawl, leaked credentials, and the operational burden of rotating secrets across repositories.

## Integration options

Aembit provides two ways to integrate with GitHub Actions:

| Option                                                                                                   | Best for           | Key benefit                                                                      |
| -------------------------------------------------------------------------------------------------------- | ------------------ | -------------------------------------------------------------------------------- |
| **[Aembit GitHub Action](#get-started)**                                                                 | Most users         | Minimal YAML Ain’t Markup Language (YAML) configuration, automatic OIDC handling |
| **[Aembit Edge Command-Line Interface (CLI)](github-edge-cli.md)** | Advanced use cases | Full CLI flexibility for custom scripts                                          |

> **Which should you choose?**
>
> Use the **Aembit GitHub Action** unless you need CLI options the action doesn’t expose. The action handles OIDC token exchange automatically with minimal configuration.

## Code comparison

The following examples show the difference between using the Aembit GitHub Action versus manually handling OIDC credential retrieval.

* With Aembit GitHub Action

  ```yaml
  - name: Get credentials from Aembit
    id: aembit
    uses: Aembit/get-credentials@v1
    with:
      client-id: 'your-client-id'
      server-host: 'api.example.com'
      server-port: '443'


  - name: Call API
    env:
      TOKEN: ${{ steps.aembit.outputs.token }}
    run: |
      curl -H "Authorization: Bearer $TOKEN" https://api.example.com/endpoint
  ```

  The Aembit GitHub Action handles OIDC token exchange, credential retrieval, and secret masking automatically. Credentials are available as [step outputs](github-actions-reference.md).

* Without the action

  To retrieve an API key and use it to call an API, you’d need to handle OIDC token exchange manually:

  ```yaml
  - name: Get OIDC Token from GitHub
    id: get-oidc
    run: |
      # Request an OIDC token from GitHub's OIDC provider
      OIDC_TOKEN=$(curl -sSf -H "Authorization: bearer $ACTIONS_ID_TOKEN_REQUEST_TOKEN" \
        "$ACTIONS_ID_TOKEN_REQUEST_URL?audience=aembit-prod")
      # Extract the token value from JSON response
      export OIDC_TOKEN=$(echo "$OIDC_TOKEN" | jq -r '.value')
      echo "::add-mask::$OIDC_TOKEN"
      echo "OIDC_TOKEN=$OIDC_TOKEN" >> $GITHUB_ENV


  - name: Request API Key from Aembit
    env:
      OIDC_TOKEN: ${{ env.OIDC_TOKEN }}
    run: |
      # Construct the API request to Aembit
      RESPONSE=$(curl -sSf -X POST "https://edge.aembit.io/api/v1/credential" \
        -H "Authorization: Bearer $OIDC_TOKEN" \
        -H "Content-Type: application/json" \
        -d '{
              "client_id": "your-client-id",
              "server_workload_host": "api.example.com",
              "server_workload_port": 443
            }')
      # Parse the API key from the response
      APIKEY=$(echo "$RESPONSE" | jq -r '.credentials.APIKEY')
      echo "::add-mask::$APIKEY"
      echo "APIKEY=$APIKEY" >> $GITHUB_ENV


  - name: Call API
    env:
      APIKEY: ${{ env.APIKEY }}
    run: |
      curl -H "Authorization: Bearer $APIKEY" https://api.example.com/endpoint
  ```

  This approach requires:

  * Knowledge of GitHub’s OIDC token endpoints and parameters
  * Familiarity with the Aembit API request/response format
  * Manual error handling and secret masking
  * Extra dependencies (such as `jq` for JSON parsing)

## Why use Aembit for GitHub Actions

Attackers commonly target Continuous Integration/Continuous Deployment (CI/CD) pipelines because they often contain credentials for accessing production systems. Traditional approaches store these secrets in GitHub’s secrets manager, creating these risks:

* **Secret sprawl** - Credentials duplicated across multiple repositories
* **Rotation burden** - Updating secrets requires changes in every repo that uses them
* **Audit gaps** - Difficult to track which workflows accessed which credentials
* **Breach exposure** - Compromised repository secrets affect all workflows using them

With Aembit, your workflows request credentials at runtime using GitHub’s OpenID Connect (OIDC) identity. Aembit validates the request against your access policies and delivers just-in-time credentials for that job.

## How it works

Instead of storing secrets in GitHub:

1. Your workflow authenticates to Aembit using GitHub’s OIDC token
2. Aembit validates the workflow identity against your Trust Provider
3. If authorized by an Access Policy, Aembit delivers the requested credential
4. The credential exists only for that job

You can revoke access centrally, see every credential request in Aembit’s audit logs, and remove stored secrets from your repositories.

## Before you begin

To use Aembit with GitHub Actions, you need:

* An [Aembit account](https://aembit.io)

* A GitHub repository with [Actions enabled](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/enabling-features-for-your-repository/managing-github-actions-settings-for-a-repository)

* The following Aembit entities configured:

  * **Client Workload** with [GitHub Identifier (ID) Token identity](../../../access-policies/client-workloads/identification/github-id-token-repository.md)
  * **Trust Provider** for [GitHub Actions](../../../access-policies/trust-providers/github-trust-provider.md)
  * **Credential Provider** for the service you want to access
  * **Server Workload** for the target service
  * **Access Policy** linking these entities

## Get started

Choose based on your experience:

* **New to Aembit?** Follow the [Guided tutorial](github-actions-tutorial.md) for step-by-step setup
* **Experienced user?** Jump to the [How-To Guide](github-actions-how-to.md) for quick configuration
* **Need parameter reference?** See the [Reference](github-actions-reference.md) for all action inputs and outputs
