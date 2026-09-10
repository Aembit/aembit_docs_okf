---
type: how-to
title: "Edge API authentication with AWS ECS"
description: "Authenticating with Aembit Edge API from an AWS ECS task using the task's IAM role"
resource: https://docs.aembit.io/dev-guide/api/edge/auth/aws-ecs/
interface: api
tags: ["auth", "edge", "api"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Edge API authentication with AWS ECS

AWS ECS tasks (including ECS Fargate) authenticate with the Aembit Edge API using the [AWS Role](../../../../user-guide/access-policies/trust-providers/aws-role-trust-provider.md) attestation method. You provide a signed AWS Security Token Service (STS) [GetCallerIdentity request](https://docs.aws.amazon.com/STS/latest/APIReference/API_GetCallerIdentity.html), signed with the task’s IAM role credentials. Aembit calls `GetCallerIdentity` to verify the signature and confirm the task’s identity.

This is the same [AWS Signature Version 4 (SigV4)](../../../../user-guide/access-policies/credential-providers/aws-sigv4.md) request signing used for [AWS IAM Role](aws-iam-role.md) authentication. ECS differs only in where the role credentials come from: ECS exposes the task role credentials through the container credentials endpoint instead of an EC2 instance metadata service.

You can optionally include the task’s base64-encoded container and task metadata in the request to enable [AWS ECS Service Name](../../../../user-guide/access-policies/client-workloads/identification/aws-ecs-service-name.md) and [AWS ECS Task Family](../../../../user-guide/access-policies/client-workloads/identification/aws-ecs-task-family.md) Client Workload identification.

## Prerequisites

To authenticate from an AWS ECS task, you must have the following:

* Your Trust Provider’s [Edge SDK Client ID](#how-to-find-your-edge-sdk-client-id) from an AWS Role Trust Provider
* An ECS task with an attached [task IAM role](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html) that the AWS Role Trust Provider’s match rules identify
* Terminal access to the running container (for example, through [ECS Exec](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs-exec.html)), or the equivalent logic running in your task
* Python 3.x with the `requests` library (for the Python method) or the `boto3` library (for the Boto3 method)

ECS provides the task role’s temporary credentials through the [container credentials endpoint](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-iam-roles.html), addressed by the `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` environment variable that ECS sets automatically.

## Authenticate from AWS ECS

To authenticate with the Aembit Edge API from an ECS task, follow these steps:

1. Retrieve the task role credentials from the ECS container credentials endpoint. ECS sets `AWS_CONTAINER_CREDENTIALS_RELATIVE_URI` automatically for tasks with an attached task role:

   ```shell
   curl "169.254.170.2$AWS_CONTAINER_CREDENTIALS_RELATIVE_URI"
   ```

   You should get output similar to:

   ```json
   {
       "RoleArn": "arn:aws:iam::123456789012:role/ecsTaskRole",
       "AccessKeyId": "ASIA42HLLFVDQQZD2PSX",
       "SecretAccessKey": "iMapV1Wn6fKCanxLwsE1RGgzUy2H7BR95zMbmKuR",
       "Token": "IQoJb3JpZ2luX2VjEO///////////wEaCXVzLWVhc3QtMSJI...",
       "Expiration": "2025-01-15T16:30:45Z"
   }
   ```

   In the next step’s Python Script method, use `AccessKeyId`, `SecretAccessKey`, and `Token` as the `ACCESS_KEY`, `SECRET_KEY`, and `SESSION_TOKEN` values. The Boto3 method discovers these credentials automatically from the task environment, so you only need this manual retrieval for the Python Script method (or to confirm the credentials are present). You’ll also need the AWS region where your task runs. Unlike Lambda, ECS doesn’t inject the region as an environment variable. Use the region of your ECS cluster, or read it from the `TaskARN` field returned by the task metadata endpoint (`curl -s "$ECS_CONTAINER_METADATA_URI_V4/task"`). The region is the fourth field of the ARN, as in `arn:aws:ecs:us-east-1:123456789012:task/...`.

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

3. *(Optional)* Get the container and task metadata to include for Client Workload identification.

   ECS exposes the [Task Metadata Endpoint version 4](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-metadata-endpoint-v4-fargate.html) through the `ECS_CONTAINER_METADATA_URI_V4` environment variable. Query the container and task metadata, then base64-encode each response using standard base64 on a single line:

   ```shell
   # Container metadata, base64-encoded
   curl -s "$ECS_CONTAINER_METADATA_URI_V4" | base64 -w 0


   # Task metadata, base64-encoded (includes the task family and, for service-launched tasks, the service name)
   curl -s "$ECS_CONTAINER_METADATA_URI_V4/task" | base64 -w 0
   ```

   Use the base64-encoded container and task metadata as the `containerMetadata` and `taskMetadata` values in the next step. These let Aembit match the [ECS Service Name](../../../../user-guide/access-policies/client-workloads/identification/aws-ecs-service-name.md) and [ECS Task Family](../../../../user-guide/access-policies/client-workloads/identification/aws-ecs-task-family.md).

4. Construct the authentication request payload using the `clientId`, the headers and region from the signing step, and—optionally—the `containerMetadata` and `taskMetadata`:

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
               "ecs": {
                   "containerMetadata": "<base64-encoded-container-metadata>",
                   "taskMetadata": "<base64-encoded-task-metadata>"
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

1. Log in to your Aembit Tenant.

2. Go to the **Trust Providers** section in the left sidebar.

3. Select the Trust Provider you want to use for Edge API authentication.

4. In the **TRUST PROVIDER** section, find the **Edge SDK Client ID** field.

5. Copy the Edge SDK Client ID to use in your authentication requests.

   ![Aembit UI Trust Provider page](https://docs.aembit.io/_astro/edge-sdk-client-id.BJB7d1dG_H47oi.webp)
