---
type: reference
title: "Edge API authentication with AWS IAM Role"
description: "Authenticating with Aembit Edge API using AWS IAM Role"
resource: https://docs.aembit.io/api-guide/edge/auth/aws-iam-role/
interface: api
tags: [auth, edge]
timestamp: 2026-06-26T13:11:59-07:00
type_inferred: true
---

# Edge API authentication with AWS IAM Role

To authenticate with the Aembit Edge API using AWS IAM Role, you need to provide a signed AWS Security Token Service (STS) [GetCallerIdentity request](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html). This authentication method uses [AWS Signature Version 4 (SigV4)](../../../user-guide/access-policies/credential-providers/aws-sigv4.md) to sign the request, proving your identity through the IAM role attached to your EC2 instance.

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To authenticate using an AWS IAM Role, you must have the following:

* Your Trust Provider’s [Edge SDK Client ID](#how-to-find-your-edge-sdk-client-id)
* EC2 instance with an attached IAM role that has `sts:GetCallerIdentity` permission
* SSH access to the instance or terminal access
* Python 3.x with the `requests` library installed (for the Python method)
* AWS credentials accessible from the instance (automatically available via instance metadata)

The IAM role attached to your EC2 instance must have at minimum the following permission:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:GetCallerIdentity",
            "Resource": "*"
        }
    ]
}
```

## Authenticate with AWS IAM Role

[Section titled “Authenticate with AWS IAM Role”](#authenticate-with-aws-iam-role)

To authenticate with the Aembit Edge API using AWS IAM Role, follow these steps:

1. Open your terminal and SSH into your EC2 instance.

2. Retrieve AWS credentials from AWS’s Instance Metadata Service (IMDS) version 2 with the following command. These credentials are automatically provided by AWS for instances with attached IAM roles:

   * Linux

     ```shell
     # Get the AWS credentials from instance metadata
     TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` \
       && ROLE_NAME=`curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/` \
       && curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/iam/security-credentials/$ROLE_NAME
     ```

   * Windows

     First, get the session token:

     ```powershell
     [string]$token = (Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token-ttl-seconds' = '21600'} `
       -Method PUT -Uri 'http://169.254.169.254/latest/api/token' -UseBasicParsing).Content
     ```

     Then get the role name:

     ```powershell
     [string]$roleName = (Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token' = $token} `
       -Uri 'http://169.254.169.254/latest/meta-data/iam/security-credentials/' -UseBasicParsing).Content
     ```

     Finally, get the credentials:

     ```powershell
     (Invoke-WebRequest -Headers @{'X-aws-ec2-metadata-token' = $token} `
       -Uri "http://169.254.169.254/latest/meta-data/iam/security-credentials/$roleName" -UseBasicParsing).Content
     ```

   You should get output similar to:

   ```json
   ...
   {
     "Code": "Success",
     "LastUpdated": "2025-01-15T10:30:45Z",
     "Type": "AWS-HMAC",
     "AccessKeyId": "ASIA42HLLFVDQQZD2PSX",
     "SecretAccessKey": "iMapV1Wn6fKCanxLwsE1RGgzUy2H7BR95zMbmKuR",
     "Token": "IQoJb3JpZ2luX2VjEO///////////wEaCXVzLWVhc3QtMSJI...",
     "Expiration": "2025-01-15T16:30:45Z"
   }
   ```

   In the next step’s Python Script method, use `AccessKeyId`, `SecretAccessKey`, and `Token` as the `ACCESS_KEY`, `SECRET_KEY`, and `SESSION_TOKEN` values. The Boto3 method discovers these credentials automatically from the instance environment, so you only need this manual retrieval for the Python Script method (or to confirm the credentials are present).

   You’ll also need the AWS region where your instance runs. Read it from the instance metadata `placement/region` path (reusing the IMDSv2 token from the previous command), or use the region your instance runs in:

   ```shell
   curl -H "X-aws-ec2-metadata-token: $TOKEN" http://169.254.169.254/latest/meta-data/placement/region
   ```

3. Generate the signed STS GetCallerIdentity request headers using one of the following methods. Both methods produce the same set of headers and the AWS region, which you’ll use to construct the authentication request payload.

   * Python Script

     Using this method, you’ll create a standalone Python script that manually implements AWS SigV4 signing.

     Create a file named `generate_sts_headers.py` with the following content. Replace the placeholder values with the AWS credentials you retrieved in the previous step:

     ```python
     import datetime, hashlib, hmac, json


     # Replace these with the AWS credentials you retrieved in the previous step
     ACCESS_KEY = "ACCESS_KEY_PLACEHOLDER"
     SECRET_KEY = "SECRET_KEY_PLACEHOLDER"
     SESSION_TOKEN = "SESSION_TOKEN_PLACEHOLDER"  # Your full session token
     REGION = "REGION_PLACEHOLDER"  # Your AWS region


     def sign(key, msg):
         return hmac.new(key, msg.encode("utf-8"), hashlib.sha256).digest()


     def getSignatureKey(key, date_stamp, regionName, serviceName):
         kDate = sign(('AWS4' + key).encode('utf-8'), date_stamp)
         kRegion = sign(kDate, regionName)
         kService = sign(kRegion, serviceName)
         kSigning = sign(kService, 'aws4_request')
         return kSigning


     # Request details
     method = 'POST'
     service = 'sts'
     host = f'{service}.{REGION}.amazonaws.com'
     endpoint = f'https://{host}/'
     content_type = 'application/x-www-form-urlencoded; charset=utf-8'
     request_parameters = "Action=GetCallerIdentity&Version=2011-06-15"


     # Create timestamp
     t = datetime.datetime.now(datetime.timezone.utc)
     amz_date = t.strftime('%Y%m%dT%H%M%SZ')
     date_stamp = t.strftime('%Y%m%d')


     # Create canonical request
     canonical_uri = '/'
     canonical_querystring = ''
     canonical_headers = f'content-type:{content_type}\nhost:{host}\nx-amz-date:{amz_date}\nx-amz-security-token:{SESSION_TOKEN}\n'
     signed_headers = 'content-type;host;x-amz-date;x-amz-security-token'
     payload_hash = hashlib.sha256(request_parameters.encode('utf-8')).hexdigest()
     canonical_request = f'{method}\n{canonical_uri}\n{canonical_querystring}\n{canonical_headers}\n{signed_headers}\n{payload_hash}'


     # Create string to sign
     algorithm = 'AWS4-HMAC-SHA256'
     credential_scope = f'{date_stamp}/{REGION}/{service}/aws4_request'
     string_to_sign = f'{algorithm}\n{amz_date}\n{credential_scope}\n{hashlib.sha256(canonical_request.encode()).hexdigest()}'


     # Calculate signature
     signing_key = getSignatureKey(SECRET_KEY, date_stamp, REGION, service)
     signature = hmac.new(signing_key, string_to_sign.encode('utf-8'), hashlib.sha256).hexdigest()


     # Create authorization header
     authorization_header = f'{algorithm} Credential={ACCESS_KEY}/{credential_scope}, SignedHeaders={signed_headers}, Signature={signature}'


     # Output the headers needed for Aembit authentication
     headers_for_aembit = {
         "Content-Type": content_type,
         "X-Amz-Date": amz_date,
         "X-Amz-Security-Token": SESSION_TOKEN,
         "Authorization": authorization_header
     }


     print("Headers for Aembit stsGetCallerIdentity:")
     print(json.dumps(headers_for_aembit, indent=2))
     print(f"\nRegion: {REGION}")
     ```

     Run the script and note the output headers and region:

     ```shell
     python3 generate_sts_headers.py
     ```

   * Boto3 (AWS SDK)

     Using this method, you’ll use the [AWS SDK for Python (Boto3)](https://aws.amazon.com/sdk-for-python/). Boto3 handles credential discovery and request signing for you.

     1. Install Boto3 if not already available:

        ```shell
        # pip
        pip3 install boto3


        # apt
        apt install python3-boto3
        ```

     2. Create a Python script named `generate_sts_headers_boto3.py` that uses Boto3’s internal signing mechanisms:

        ```python
        import boto3
        import json
        from botocore.auth import SigV4Auth
        from botocore.awsrequest import AWSRequest


        # Boto3 automatically uses the credentials available in your AWS environment
        session = boto3.Session()
        credentials = session.get_credentials()


        # Set your region
        region = 'REGION_PLACEHOLDER'  # Change to your AWS region


        # Create the STS GetCallerIdentity request
        request = AWSRequest(
            method='POST',
            url=f'https://sts.{region}.amazonaws.com/',
            data='Action=GetCallerIdentity&Version=2011-06-15',
            headers={
                'Content-Type': 'application/x-www-form-urlencoded; charset=utf-8'
            }
        )


        # Sign the request using SigV4
        SigV4Auth(credentials, "sts", region).add_auth(request)


        # Extract headers for Aembit authentication
        headers_for_aembit = {
            'Content-Type': request.headers.get('Content-Type'),
            'X-Amz-Date': request.headers.get('X-Amz-Date'),
            'X-Amz-Security-Token': request.headers.get('X-Amz-Security-Token'),
            'Authorization': request.headers.get('Authorization')
        }


        print("Headers for Aembit stsGetCallerIdentity:")
        print(json.dumps(headers_for_aembit, indent=2))
        print(f"\nRegion: {region}")
        ```

     3. Run the script:

        ```shell
        python3 generate_sts_headers_boto3.py
        ```

4. Construct the authentication request payload using the `clientId`, and the headers and region from the signing step:

   ```json
   {
       "clientId": "<edge-sdk-client-id>",
       "client": {
           "aws": {
               "stsGetCallerIdentity": {
                   "headers": {
                       "Content-Type": "application/x-www-form-urlencoded; charset=utf-8",
                       "X-Amz-Date": "20250115T103045Z",
                       "X-Amz-Security-Token": "IQoJb3JpZ2luX2VjEO...",
                       "Authorization": "AWS4-HMAC-SHA256 Credential=ASIA42HLLFVDQQZD2PSX/20250115/us-east-1/sts/aws4_request, SignedHeaders=content-type;host;x-amz-date;x-amz-security-token, Signature=abc123..."
                   },
                   "region": "<region-from-signing-step>"
               }
           }
       }
   }
   ```

5. Send the authentication request to your Aembit Edge API endpoint:

   ```shell
   curl --location 'https://<your-aembit-edge-url>/edge/v1/auth' \
   --header 'Content-Type: application/json' \
   --data '{
       "clientId": "your-edge-sdk-client-id",
       "client": {
           "aws": {
               "stsGetCallerIdentity": {
                   "headers": {
                       "Content-Type": "application/x-www-form-urlencoded; charset=utf-8",
                       "X-Amz-Date": "20250115T103045Z",
                       "X-Amz-Security-Token": "IQoJb3JpZ2luX2VjEO...",
                       "Authorization": "AWS4-HMAC-SHA256 Credential=ASIA42HLLFVDQQZD2PSX/20250115/us-east-1/sts/aws4_request, SignedHeaders=content-type;host;x-amz-date;x-amz-security-token, Signature=abc123..."
                   },
                   "region": "us-east-1"
               }
           }
       }
   }'
   ```

   When successful, you’ll receive output similar to:

   ```json
   {
       "accessToken": "eyJhbGciOiJSUzI1NiIsImtpZCI6IkpyR3JLQ0x6RVFN...",
       "tokenType": "Bearer",
       "expiresIn": 3600
   }
   ```

6. Use the `accessToken` as the `bearerToken` in subsequent API calls to authenticate your requests. This token is valid for the duration specified in `expiresIn` (in seconds).

## How to find your Edge SDK Client ID

[Section titled “How to find your Edge SDK Client ID”](#how-to-find-your-edge-sdk-client-id)

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
