---
type: how-to
title: "Kubernetes Service Account trust provider"
description: "This page describes the steps required to configure the Kubernetes Service Account Trust Provider."
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider/
interface: web-ui
tags: [trust-provider, access-policy]
timestamp: 2025-04-14T12:09:34-07:00
type_inferred: true
---

# Kubernetes Service Account trust provider


The Kubernetes Service Account Trust Provider supports attestation of Client Workloads and Agent Controller identities in a Kubernetes environment (either self-hosted or managed by cloud providers - [AWS EKS](https://aws.amazon.com/eks/), [Azure AKS](https://azure.microsoft.com/en-us/products/kubernetes-service), [GCP GKE](https://cloud.google.com/kubernetes-engine?hl=en)).

## Match rules

[Section titled “Match rules”](#match-rules)

The following match rules are available for this Trust Provider type:

* iss
* kubernetes.io { namespace }
* kubernetes.io { pod { name } }
* kubernetes.io { serviceaccount { name } }
* sub

| Data                                      | Description                   | Example                                        |
| ----------------------------------------- | ----------------------------- | ---------------------------------------------- |
| iss                                       | Kubernetes Cluster Issuer URL | <https://kubernetes.default.svc.cluster.local> |
| kubernetes.io { namespace }               | Pod namespace                 | default                                        |
| kubernetes.io { pod { name } }            | Pod name                      | example-app                                    |
| kubernetes.io { serviceaccount { name } } | Service Account name          | default                                        |
| sub                                       | Service Account token subject | system:serviceaccount:default:default          |

## Additional configurations

[Section titled “Additional configurations”](#additional-configurations)

Aembit requires a Kubernetes cluster public key to validate the Service Account token used by this trusted provider.

The majority of cloud providers expose an OIDC endpoint that enables automatic retrieval of the Kubernetes cluster public key.

Note

There are multiple ways to retrieve the OIDC endpoint (via UI, CLI, API, etc.) The steps below use the CLI approach; however, select the way that is most appropriate for your organization.

### AWS EKS

[Section titled “AWS EKS”](#aws-eks)

* Ensure your AWS CLI is installed, configured, and authenticated.

* Execute the following command:

```shell
aws eks describe-cluster --name \<cluster_name\> --query "cluster.identity.oidc.issuer" --output text
```

* Paste the response in **OIDC Endpoint** field.

### GCP GKE

[Section titled “GCP GKE”](#gcp-gke)

* Ensure your GCP CLI is installed, configured, and authenticated.

* Execute the following command:

```shell
gcloud container clusters describe \<cluster_name\> --region=\<cluster_region\> --format="value(selfLink)"
```

* Paste the response in **OIDC Endpoint** field.

## Azure AKS

[Section titled “Azure AKS”](#azure-aks)

* Ensure your Azure CLI is installed, configured, and authenticated.

* Execute the following command:

```shell
az aks show --resource-group \<resource_group_name\> --name \<cluster_name\> --query "oidcIssuerProfile.issuerUrl" -o tsv
```

* Paste the response in **OIDC Endpoint** field.

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
* [Kubernetes Namespace](../client-workloads/identification/kubernetes-namespace.md)
* [Kubernetes Pod Name](../client-workloads/identification/kubernetes-pod-name.md)
* [Kubernetes Pod Name Prefix](../client-workloads/identification/kubernetes-pod-name-prefix.md)
* [Kubernetes Service Account Name](../client-workloads/identification/kubernetes-service-account-name.md)
* [Kubernetes Service Account UID](../client-workloads/identification/kubernetes-service-account-uid.md)
