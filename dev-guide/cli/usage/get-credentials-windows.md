---
type: how-to
title: "Getting credentials with Aembit CLI on Windows IoT Enterprise"
description: "A guide to getting credentials with Aembit CLI on Windows IoT Enterprise using OIDC ID Token Trust Providers"
resource: https://docs.aembit.io/dev-guide/cli/usage/get-credentials-windows/
interface: cli
tags: ["usage", "cli"]
timestamp: 2026-09-09T09:33:20-07:00
---

# Getting credentials with Aembit CLI on Windows IoT Enterprise

Follow the steps on this page to use the Aembit CLI on Windows to retrieve credentials using an OIDC ID Token Trust Provider with a PowerShell-generated token.

This procedure uses a PowerShell script to generate an OIDC token for authentication with Aembit. The script creates a signed JWT using a symmetric key, which you’ll configure in your OIDC ID Token Trust Provider.

> **PowerShell required**
>
> This procedure requires Windows PowerShell. The Windows Command Prompt (`cmd.exe`) doesn’t work with this approach.
>
> Aembit also recommends avoiding PowerShell Integrated Scripting Environment (ISE), as it may not handle certain commands correctly. Use the standard PowerShell terminal instead.

## Prerequisites

Before you can retrieve credentials, ensure you have the following:

* Windows Server 2019 or Windows IoT Enterprise 2021 LTSC (see [Supported operating systems](../overview.md#supported-operating-systems))
* [Aembit CLI installed](setup.md)
* Access to your Aembit Tenant with your fully configured Access Policy
* An [OIDC ID Token Trust Provider](../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) using the **Symmetric Key Attestation Method**
* A [supported Credential Provider](../overview.md#supported-credential-providers)
* The hostname and port of the Server Workload you want to access

Your Credential Provider determines which names you can pass to `--credential-names`, so check [Credential name support](../credential-names.md) before you rename a credential.

> **Environment variable and command option priority**
>
> You can configure the Aembit CLI using both environment variables and command options. Command options take precedence, overriding any corresponding environment variables.
>
> For example, if you’ve set a value with the `--client-id` option, Aembit CLI uses that over the `AEMBIT_CLIENT_ID` environment variable. This lets you establish a default configuration with environment variables and override specific settings for individual commands as needed.

## Get credentials using PowerShell script

This procedure requires you to move between your PowerShell terminal and your Aembit Tenant. You run a PowerShell script to generate a symmetric key, configure that key in your OIDC Trust Provider, and run the script again to retrieve credentials.

You can customize the command with additional options available in the [Optional configurations](#optional-configurations) section.

To retrieve credentials using a PowerShell-generated OIDC token, follow these steps:

1. Log into your Aembit Tenant.

2. Follow the steps in [Find your Edge SDK Client ID](../../../user-guide/access-policies/trust-providers/get-edge-sdk-client-id.md) to obtain your Edge SDK Client ID.

3. Ensure you’ve configured your [OIDC ID Token Trust Provider](../../../user-guide/access-policies/trust-providers/oidc-id-token-trust-provider.md) with the **Symmetric key** attestation method. Leave the **Symmetric Key** field empty initially; you’ll populate this after running the PowerShell script in the coming steps.

4. Identify the **Hostname** and **Port** of the Server Workload you want the credential for.

   You can do this by checking the Server Workload’s configuration or by checking the Access Policy that applies to the Workload in your Aembit Tenant.

5. Open your terminal that has Aembit CLI installed.

6. Create the PowerShell script by copying the following code to a file named `get-credentials.ps1` in the same directory as `aembit.exe`:

   Complete PowerShell script (click to expand)

   ```powershell
   # Function to convert byte array to Base64URL string (per JWT spec RFC 7515)
   function To-Base64UrlString {
       param ([byte[]]$bytes)
       $base64 = [Convert]::ToBase64String($bytes)
       # Base64URL encoding replaces unsafe URL characters and removes padding.
       return $base64.TrimEnd('=') -replace '\+', '-' -replace '/', '_'
   }


   # Function to Base64URL-encode a JSON object
   function ConvertTo-Base64Url {
       param ($inputObject)
       $json = $inputObject | ConvertTo-Json -Compress
       $bytes = [System.Text.Encoding]::UTF8.GetBytes($json)
       return To-Base64UrlString -bytes $bytes
   }


   # Function to compute HMAC-SHA256 signature using a shared secret
   function Get-HMACSHA256 {
       param (
           [string]$data,
           [string]$key
       )
       $hmac = New-Object System.Security.Cryptography.HMACSHA256
       $hmac.Key = [System.Text.Encoding]::UTF8.GetBytes($key)
       # This is the key you need for the Trust Provider
       $base64EncodedKey = [Convert]::ToBase64String($hmac.Key)
       Write-Host "Base64 encoded key: $base64EncodedKey"
       $dataBytes = [System.Text.Encoding]::UTF8.GetBytes($data)
       $hashBytes = $hmac.ComputeHash($dataBytes)
       return To-Base64UrlString -bytes $hashBytes
   }


   # Function to generate a signed JWT token
   function New-JwtToken {
       param (
           [hashtable]$Header,
           [hashtable]$Payload,
           [string]$Secret
       )
       $headerEncoded = ConvertTo-Base64Url $Header
       $payloadEncoded = ConvertTo-Base64Url $Payload
       $unsignedToken = "$headerEncoded.$payloadEncoded"
       $signatureEncoded = Get-HMACSHA256 -data $unsignedToken -key $Secret
       return "$unsignedToken.$signatureEncoded"
   }


   # Script configuration
   $header = @{
       alg = "HS256"
       typ = "JWT"
   }
   $payload = @{
       iss = "self"
       sub = "self"
       aud = "https://<tenantId>.aembit.io"
       exp = [DateTimeOffset]::UtcNow.AddMinutes(60).ToUnixTimeSeconds()
       iat = [DateTimeOffset]::UtcNow.ToUnixTimeSeconds()
   }


   # Shared secret for signing the token
   $secret = "<secret>"


   # Aembit CLI parameters
   $clientId = "<clientId>"
   $serverHost = "<host>"
   $serverPort = "<port>"
   $credentialNames = "<credentialName>"
   $deploymentModel = "vm"


   # Set Client Workload Identifier environment variable
   $env:CLIENT_WORKLOAD_ID = "<clientWorkloadId>"


   # Generate the OIDC token
   $jwt = New-JwtToken -Header $header -Payload $payload -Secret $secret
   Write-Host "OIDC token: $jwt"
   $idToken = $jwt


   # Construct arguments for aembit.exe
   $args = @(
       "credentials", "get",
       "--client-id", $clientId,
       "--server-workload-host", $serverHost,
       "--server-workload-port", $serverPort,
       "--credential-names", $credentialNames,
       "--id-token", $idToken,
       "--deployment-model", $deploymentModel
   )


   # Execute the Aembit CLI
   $result = (& ".\aembit.exe" @args)
   $exitCode = $LASTEXITCODE


   if ($exitCode -eq 0) {
       Invoke-Expression $result
       Write-Host "Retrieved credential: $env:<credentialName>"
   } else {
       Write-Host "Failed to get credentials."
   }
   ```

7. Configure the required script variables by updating these values in `get-credentials.ps1`:

   **Required changes:**

   * `<tenantId>`: Change to your tenant ID (for example, `a12bc3`)
   * `<clientId>`: Update with your Edge SDK Client ID from the OIDC Trust Provider
   * `<clientWorkloadId>`: Set to your Client Workload’s Aembit Client ID, not the workload’s own resource ID
   * `<host>`: Update with your Server Workload hostname
   * `<port>`: Update with your Server Workload port

   **Configure credential type:**

   * Single-value credentials

     For [Credential Providers](../overview.md#supported-credential-providers) that output a single credential value.

     **The default credential name is set to the `TOKEN` environment variable**. If you’d like to use a different name, update the `<credentialName>` variable in the script.

     ```powershell
     $credentialNames = "MY_ACCESS_TOKEN"
     ```

   * Username & Password

     For the [Username & Password](../../../user-guide/access-policies/credential-providers/username-password.md) Credential Provider. This Credential Provider outputs two separate credentials that must use the names `USERNAME` and `PASSWORD`.

     ```powershell
     $credentialNames = "USERNAME,PASSWORD"
     ```

   * Vault Private Network Access

     Use this approach with the [Vault Client Token](../../../user-guide/access-policies/credential-providers/vault-client-token.md) Credential Provider configured for private network access.

     In this configuration, the Credential Provider outputs a credential named `PROXY_CREDENTIAL`.

     ```powershell
     $credentialNames = "PROXY_CREDENTIAL"
     ```

8. Run the script once to generate the symmetric key:

   ```powershell
   .\get-credentials.ps1
   ```

   The script outputs a “Base64 encoded key” at the beginning. Copy this value.

9. Update your OIDC Trust Provider with the generated symmetric key:

   * Return to your Aembit Tenant
   * Navigate to your OIDC ID Token Trust Provider
   * Paste the Base64 encoded key from the previous step into the **Symmetric key** field
   * Save the Trust Provider configuration

10. Run the script again to retrieve your credentials:

    ```powershell
    .\get-credentials.ps1
    ```

## Example configuration

Here’s a complete example showing the key script variables configured for a real scenario:

```powershell
# JWT Payload with actual tenant
$payload = @{
    iss = "self"
    sub = "self"
    aud = "https://a12bc3.aembit.io" # Your actual tenant URL
    exp = [DateTimeOffset]::UtcNow.AddMinutes(60).ToUnixTimeSeconds()
    iat = [DateTimeOffset]::UtcNow.ToUnixTimeSeconds()
}


# Aembit CLI Parameters
$clientId = "aembit:useast2:a12bc3:identity:oidc_id_token:63ab7be6-9785-4a14-be1c-2acf0253070b"
$serverHost = "api.example.com"
$serverPort = "443"
$credentialNames = "API_TOKEN"
$env:CLIENT_WORKLOAD_ID = "1114eab7-e099-41bf-af6d-546a97021335"
```

## Expected output

When the script runs successfully, you’ll see output similar to:

```shell
Base64 encoded key: eW91ci0yNTYtYml0LXNlY3JldA==
OIDC token: eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
[Debug output from Aembit CLI]
Retrieved credential: your-api-token-value
```

## Optional configurations

You can customize the script behavior with these optional changes:

**Custom signing secret:** To use a different signing secret, change the `<secret>` variable:

```powershell
$secret = "my-custom-secret-key"
```

Remember to run the script once to get the new Base64 encoded key, then update your Trust Provider.

**Debug logging:** To enable debug logging for troubleshooting, add the `--log-level` flag to the `$args` array:

```powershell
$args = @(
    "credentials", "get",
    "--client-id", $clientId,
    "--server-workload-host", $serverHost,
    "--server-workload-port", $serverPort,
    "--credential-names", $credentialNames,
    "--id-token", $idToken,
    "--log-level", "debug",
    "--deployment-model", $deploymentModel
)
```

**Resource sets:** If you’re using resource sets, add the `--resource-set-id` flag to the `$args` array:

```powershell
$resourceSetId = "<your-resource-set-id>"


$args = @(
    "credentials", "get",
    "--client-id", $clientId,
    "--server-workload-host", $serverHost,
    "--server-workload-port", $serverPort,
    "--credential-names", $credentialNames,
    "--resource-set-id", $resourceSetId,
    "--id-token", $idToken,
    "--deployment-model", $deploymentModel
)
```

## Next steps

Once you’ve retrieved the credentials, they’re available as environment variables in your current PowerShell session. You can use them directly in your scripts or applications.

**Example usage:**

```powershell
# Use the credential to make an API call
Invoke-RestMethod -Uri "https://api.example.com/data" -Headers @{
    "Authorization" = "Bearer $env:API_TOKEN"
}


# Or with username/password credentials
$auth = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes("$env:USERNAME:$env:PASSWORD"))
Invoke-RestMethod -Uri "https://api.example.com/secure" -Headers @{
    "Authorization" = "Basic $auth"
}
```

**Important notes:**

* Run the script from a standard Windows PowerShell terminal (not PowerShell ISE or `cmd.exe`)
* Credentials are only available in the current PowerShell session
* To use credentials in a different session, run the script again
* For troubleshooting common issues, see the [CLI troubleshooting guide](../troubleshooting.md)
