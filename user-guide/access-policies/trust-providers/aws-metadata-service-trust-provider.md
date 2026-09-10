---
type: reference
title: "AWS Metadata Service trust provider"
description: "This page describes the steps required to configure an AWS Metadata Service Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/aws-metadata-service-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# AWS Metadata Service trust provider


The AWS Metadata Service Trust Provider supports attestation of Client Workloads and Agent Controller identities in [AWS](https://aws.amazon.com/) environments (running either directly on EC2 instances or on managed [AWS EKS](https://aws.amazon.com/eks/)).

The AWS Metadata Service Trust Provider relies on the [AWS Metadata Service](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-instance-metadata.html) for instance identity document.

## Match rules

The following match rules are available for this Trust Provider type:

* accountId
* architecture
* availabilityZone
* billingProducts
* imageId
* instanceId
* instanceType
* kernelId
* marketplaceProductCodes
* pendingTime
* privateIp
* ramdiskId
* region
* version

Please refer to the [AWS documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-identity-documents.html) for a detailed description of match rule fields available in the identity document.

## Additional configurations

Aembit requires one of AWS’s public certificates to verify the identity document signature. Please download the certificate from the [AWS public certificate page](https://docs.aws.amazon.com/es_en/AWSEC2/latest/UserGuide/regions-certs.html) for the region that hosts your Client Workloads. Please use certificates under the RSA tabs on the AWS documentation page and paste the appropriate certificate into **Certificate** field on the **Trust Provider** page.

## Related

**Compatible credential providers**

* [API Key](../credential-providers/api-key.md)
* [AWS STS Federation](../credential-providers/aws-security-token-service-federation.md)
* [HashiCorp Vault Client Token](../credential-providers/vault-client-token.md)
* [JSON Web Token (JWT)](../credential-providers/json-web-token.md)
* [OAuth 2.0 Client Credentials](../credential-providers/oauth-client-credentials.md)
* [OIDC ID Token](../credential-providers/oidc-id-token.md)
* [Username & Password](../credential-providers/username-password.md)

**Client workload identifiers this trust supports**

* [Aembit Client ID](../client-workloads/identification/aembit-client-id.md)
* [AWS Account ID](../client-workloads/identification/aws-account-id.md)
* [AWS EC2 Instance ID](../client-workloads/identification/aws-ec2-instance-id.md)
* [AWS Region](../client-workloads/identification/aws-region.md)
* [Hostname](../client-workloads/identification/hostname.md)
* [Process Command Line](../client-workloads/identification/process-command-line.md)
* [Process Name](../client-workloads/identification/process-name.md)
* [Process Path](../client-workloads/identification/process-path.md)
* [Process User Name](../client-workloads/identification/process-user-name.md)
* [Source IP Address](../client-workloads/identification/source-ip.md)
