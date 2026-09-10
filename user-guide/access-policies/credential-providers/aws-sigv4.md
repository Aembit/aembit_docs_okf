---
type: explanation
title: "How Aembit uses AWS SigV4 and SigV4a"
description: "How Aembit's Credential Provider for AWS STS works with the AWS SigV4 and Sigv4a request signing protocols"
resource: https://docs.aembit.io/user-guide/access-policies/credential-providers/aws-sigv4/
tags: ["credential-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# How Aembit uses AWS SigV4 and SigV4a

AWS Signature Version 4 (SigV4) and Signature Version 4a (SigV4a) are AWS request signing protocols. Aembit uses these protocols to sign HTTP requests from Client Workloads to AWS services. Credentials come from Aembit’s [AWS STS Credential Provider](aws-security-token-service-federation.md). During authentication, SigV4 ensures requests are authentic, unaltered in transit, and not replayed.

## SigV4 versions

SigV4 has two versions:

* [SigV4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html) is AWS’s standard signing process. It requires that you specify the exact AWS region where you’re sending a request (such as `us-east-1`, `us-east-2`). AWS scopes the signing key and signature to that specific region. AWS requires a new signature if you send the same request to a service in a different region. For most requests to [AWS regional endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#regional-endpoints), AWS uses SigV4.

* [SigV4a](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_sigv.html#how-sigv4a-works) extends SigV4 to support multi-region AWS services. Use SigV4a when you route a request across multiple AWS regions. Instead of specifying a single region in the signature, SigV4a uses a region wildcard (\*), allowing the signature to be valid across all AWS regions. For requests to [AWS global service endpoints](https://docs.aws.amazon.com/general/latest/gr/rande.html#global-endpoints) or any service that supports cross-region requests, AWS requires SigV4a.

## SigV4 version selection

Aembit automatically determines whether to use SigV4 or SigV4a when a Client Workload uses an AWS STS Credential Provider to access AWS services. It works like this:

* Aembit uses **SigV4** when a Server Workload's hostname includes a region (such as `us-east-1` or `us-east-2`), scoping the signature to only that region.

* Aembit uses **SigV4a** when the Server Workload’s hostname doesn’t include a region (S3 Multi-Region Access Points or other global AWS services), which allows the signature to work across AWS regions.

Aembit performs this selection automatically based on the hostname structure, following AWS’s standard endpoint formats. You don’t need to make configuration changes to benefit from this. Your existing AWS STS Credential Providers automatically gain support for SigV4a where applicable.

## Workload identity and service access separation in AWS

When working with Aembit Trust Providers and

Credential Providers in AWS environments, it’s important to understand the roles each of these play. Aembit uses Trust Providers to verify who a workload is, and Credential Providers to control what AWS services that workload can access.

1. Trust Providers (like the [AWS Role Trust Provider](../trust-providers/aws-role-trust-provider.md)) verify who a workload is by confirming the AWS environment it’s running in and the IAM Role it’s using.

2. Once Aembit verifies the workload’s identity, the [AWS STS Credential Provider](aws-security-token-service-federation.md) retrieves temporary AWS credentials for the workload, tied to the IAM Role verified by the Trust Provider.

3. When the workload makes API requests to AWS services like S3, Lambda, or SQS, Aembit’s Agent Proxy automatically signs those requests using AWS SigV4 for regional services, or SigV4a for global or multi-region services.

This clear separation makes sure that:

* Only attested workloads receive AWS credentials.

* Aembit secures all AWS service access using temporary credentials, eliminating the need for long-lived secrets.

* Aembit automatically applies the correct SigV4 or SigV4a signing process based on the destination service and hostname.

## Choosing the right Credential Provider for AWS environments

Aembit offers two Credential Providers commonly used in AWS environments, each serving different purposes:

* **[AWS STS Federation](aws-security-token-service-federation.md)** - Use this Credential Provider to access AWS services (S3, EC2, Lambda, DynamoDB, etc.). It generates temporary credentials and signs requests using SigV4/SigV4a, which the AWS API requires for secure authentication.

* **[AWS Secrets Manager](aws-secrets-manager.md)** - Use this Credential Provider to retrieve secrets stored in AWS Secrets Manager for accessing non-AWS services. This includes databases (RDS PostgreSQL/MySQL, Redshift, ElastiCache Redis) and third-party APIs that use Bearer tokens, API keys, or username/password authentication.

AWS Secrets Manager can store AWS access keys, but Aembit’s Credential Provider for Secrets Manager doesn’t support SigV4 signing. For AWS service access, always use AWS STS Federation.

## S3 upload support

Aembit’s Agent Proxy enables secure, transparent support for AWS S3 upload requests, addressing the unique signing and credential injection requirements of S3. S3 uploads are challenging because:

* **Complex signing:** S3 requires signing the HTTP message body, and for large files, AWS uses “rolling signatures” that sign each chunk individually.
* **Client-side signing:** Many AWS SDKs sign requests before they reach the proxy, so the proxy must detect and erase these signatures and re-sign the request with injected credentials.
* **Streaming support:** The Agent Proxy must stream and sign large payloads.

Aembit handles these challenges transparently:

* The Agent Proxy detects the client’s signing method (using the `x-amz-content-sha256` header).
* It erases any pre-existing client signatures and applies a valid SigV4 signature on the fly, including support for rolling signatures on chunked uploads.
* Most cases don’t require special client-side configuration.

Aembit supports all S3 signing methods, including unsigned payload modes, standard streaming signatures, rolling signatures with trailers, and ECDSA signatures.

Supported `x-amz-content-sha256` header values

| Signing Method                                     | Supported |
| -------------------------------------------------- | --------- |
| Actual payload checksum value                      | ✅         |
| `UNSIGNED-PAYLOAD`                                 | ✅         |
| `STREAMING-UNSIGNED-PAYLOAD-TRAILER`               | ✅         |
| `STREAMING-AWS4-HMAC-SHA256-PAYLOAD`               | ✅         |
| `STREAMING-AWS4-HMAC-SHA256-PAYLOAD-TRAILER`       | ✅         |
| `STREAMING-AWS4-ECDSA-P256-SHA256-PAYLOAD`         | ✅         |
| `STREAMING-AWS4-ECDSA-P256-SHA256-PAYLOAD-TRAILER` | ✅         |

## About request compression

Agent Proxy doesn’t support streaming payload signing for S3 requests that use request compression. This limitation only affects deployments that have explicitly enabled request compression in their AWS SDK clients, which isn’t enabled by default.

> **Note**
>
> Agent Proxy does support the `aws-chunked` Content-Encoding, which is the standard encoding for S3 streaming uploads.

### Workaround: turn off request compression

To avoid this limitation, turn off request compression in your AWS SDK client. You can set the `AWS_DISABLE_REQUEST_COMPRESSION` environment variable, or configure it in code:

* Environment variable

  ```shell
  export AWS_DISABLE_REQUEST_COMPRESSION=true
  ```

* Python (boto3)

  ```python
  from botocore.config import Config


  config = Config(disable_request_compression=True)
  client = boto3.client('s3', config=config)
  ```

* JavaScript

  ```javascript
  const client = new S3Client({ disableRequestCompression: true });
  ```

* C# (.NET)

  ```csharp
  var s3Config = new AmazonS3Config { DisableRequestCompression = true };
  var s3Client = new AmazonS3Client(s3Config);
  ```

* Java

  ```java
  S3ClientBuilder.standard().disableRequestCompression(true).build();
  ```

* Go

  ```go
  cfg, _ := config.LoadDefaultConfig(context.TODO())
  cfg.DisableRequestCompression = true
  ```

Alternatively, you can use `UNSIGNED-PAYLOAD` mode for single-chunk uploads or `STREAMING-UNSIGNED-PAYLOAD-TRAILER` for large uploads where payload integrity verification isn’t required.

## Known limitations

Agent Proxy has the following limitations when processing S3 upload requests.

### Pre-signed URLs

Aembit doesn’t support AWS pre-signed URLs. Pre-signed URLs include signing parameters in the URL query string rather than in HTTP headers, which is a different signing mechanism than the header-based SigV4/SigV4a signing that Aembit’s Agent Proxy handles.

If your application requires pre-signed URLs for use cases like generating shareable S3 download links, you must generate those URLs using AWS credentials obtained outside of Aembit’s credential injection flow.
