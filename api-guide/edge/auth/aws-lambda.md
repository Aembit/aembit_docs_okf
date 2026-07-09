---
type: reference
title: "Edge API authentication with AWS Lambda"
description: "Authenticating with Aembit Edge API from an AWS Lambda function using the function's execution role"
resource: https://docs.aembit.io/api-guide/edge/auth/aws-lambda/
interface: api
tags: [auth, edge]
timestamp: 2026-06-16T09:59:40-07:00
type_inferred: true
---

# Edge API authentication with AWS Lambda

AWS Lambda functions authenticate with the Aembit Edge API using the [AWS Role](../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) attestation method. You provide a signed AWS Security Token Service (STS) [GetCallerIdentity request](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html), signed with the function’s execution role credentials. Aembit calls `GetCallerIdentity` to verify the signature and confirm the function’s identity.

This is the same [AWS Signature Version 4 (SigV4)](../../../user-guide/access-policies/credential-providers/aws-sigv4.md) request signing used for [AWS IAM Role](aws-iam-role.md) authentication. Lambda differs only in where the role credentials come from: AWS injects them into the function’s runtime environment instead of an EC2 instance metadata service.

You can optionally include the function’s ARN in the request to enable [AWS Lambda ARN Client Workload identification](../../../user-guide/access-policies/client-workloads/identification/aws-lambda-arn.md).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To authenticate from an AWS Lambda function, you must have the following:

* Your Trust Provider’s [Edge SDK Client ID](#how-to-find-your-edge-sdk-client-id) from an AWS Role Trust Provider
* A Lambda function with an attached execution role that the AWS Role Trust Provider’s match rules identify
* Python 3.x available in your function’s runtime or build, with the `requests` library (for the Python method) or the `boto3` library (for the Boto3 method)

AWS automatically provides the execution role’s temporary credentials to your function through the [runtime environment variables](https://docs.aws.amazon.com/lambda/latest/dg/configuration-envvars.html#configuration-envvars-runtime) `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, and `AWS_SESSION_TOKEN`.

## Authenticate from AWS Lambda

[Section titled “Authenticate from AWS Lambda”](#authenticate-from-aws-lambda)

To authenticate with the Aembit Edge API from a Lambda function, follow these steps:

1. Retrieve the execution role credentials from the function’s runtime environment variables. AWS sets these automatically for the function’s execution role:

   ```python
   import os


   ACCESS_KEY = os.environ["AWS_ACCESS_KEY_ID"]
   SECRET_KEY = os.environ["AWS_SECRET_ACCESS_KEY"]
   SESSION_TOKEN = os.environ["AWS_SESSION_TOKEN"]
   REGION = os.environ["AWS_REGION"]
   ```

   If you’re verifying the flow interactively, you can print the same values from within the function’s environment:

   ```shell
   echo "$AWS_ACCESS_KEY_ID"
   echo "$AWS_SECRET_ACCESS_KEY"
   echo "$AWS_SESSION_TOKEN"
   echo "$AWS_REGION"
   ```

2. Generate the signed STS GetCallerIdentity request headers using one of the following methods. Both methods produce the same set of headers and the AWS region, which you’ll use to construct the authentication request payload.

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

3. *(Optional)* Get the function ARN to include for Client Workload identification.

   AWS provides the fully qualified function ARN in the [context object](https://docs.aws.amazon.com/lambda/latest/dg/python-context.html) passed to your handler:

   ```python
   def handler(event, context):
       function_arn = context.invoked_function_arn
       # arn:aws:lambda:<region>:<account-id>:function:<function-name>
   ```

   The ARN uses the format `arn:aws:lambda:<region>:<account-id>:function:<function-name>`. For details on qualified versus unqualified ARNs, see [AWS Lambda ARN](../../../user-guide/access-policies/client-workloads/identification/aws-lambda-arn.md).

4. Construct the authentication request payload using the `clientId`, the headers and region from the signing step, and—optionally—the function `arn`:

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
               },
               "lambda": {
                   "arn": "<lambda-function-arn>"
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
