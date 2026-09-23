---
type: how-to
title: "Select among multiple Credential Providers with the Edge SDK"
description: "Pass an Access Key ID selector as connection metadata so the Edge SDK picks one of the AWS STS Federation Credential Providers in an Access Policy."
resource: https://docs.aembit.io/dev-guide/sdk/edge/multiple-credential-providers/
interface: sdk
tags: ["edge", "sdk"]
timestamp: 2026-09-22T15:17:29-07:00
---

# Select among multiple Credential Providers with the Edge SDK

An Access Policy can hold multiple AWS STS Federation Credential Providers, each with its own **Access Key ID selector**. For how Aembit uses the selector, see [Using multiple AWS STS Federation Credential Providers](../../../user-guide/access-policies/credential-providers/aws-security-token-service-multiple.md). Pass the selector as connection metadata when you request the credential, and Aembit returns temporary AWS credentials for the IAM role of the matching Credential Provider. Connection metadata requires Edge SDK 1.34.0 or later.

* TypeScript

  **select-provider.ts**

  ```typescript
  const credential = await client.getCredential({
    server: { host: "s3.amazonaws.com", port: 443 },
    credentialType: "AwsStsFederation",
    connectionMetadata: { accessKeyId: "AKIADUMMYFORROLEA" },
  });


  const { awsAccessKeyId, awsSecretAccessKey, awsSessionToken } = credential.data;
  ```

* Python

  **select\_provider.py**

  ```python
  from aembit_edge import CredentialServerRef, GetCredentialInput


  credential_input = GetCredentialInput(
      server=CredentialServerRef(host="s3.amazonaws.com", port=443),
      credential_type="AwsStsFederation",
      connection_metadata={"accessKeyId": "AKIADUMMYFORROLEA"},
  )
  result = client.get_credential(credential_input)


  aws_access_key_id = result.data["awsAccessKeyId"]
  aws_secret_access_key = result.data["awsSecretAccessKey"]
  aws_session_token = result.data["awsSessionToken"]
  ```

Selector values contain uppercase letters and numbers only, up to 256 characters, and must match the value saved in the Credential Provider’s mapping exactly. Omit the connection metadata when the Access Policy has a single AWS STS Federation Credential Provider. A selector that matches no Credential Provider in the Access Policy fails the request.

Connection metadata also carries the selectors for [multiple JSON Web Token (JWT) Credential Providers](../../../user-guide/access-policies/credential-providers/json-web-token-multiple.md). The [Edge API credentials endpoint](../../api/edge/endpoints/credentials.md#connection-metadata) lists every field.
