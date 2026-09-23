---
type: how-to
title: "Kubernetes Service Account Trust Provider"
description: "How to configure a Kubernetes Service Account Trust Provider to attest workloads running in Kubernetes"
resource: https://docs.aembit.io/user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider/
interface: web-ui
tags: ["trust-provider", "access-policy"]
timestamp: 2026-09-22T16:01:25-04:00
---

# Kubernetes Service Account Trust Provider

The Kubernetes Service Account Trust Provider validates the Service Account token that Kubernetes issues to a pod. Use this Trust Provider to attest Client Workloads and Agent Controllers that run in any Kubernetes cluster. The cluster can be self-hosted or managed by a cloud provider such as Amazon Elastic Kubernetes Service (EKS), Azure Kubernetes Service (AKS), or Google Kubernetes Engine (GKE).

Kubernetes signs each Service Account token with the cluster’s private key. This Trust Provider verifies that signature, reads the pod’s namespace, name, and Service Account from the token, and evaluates your Access Policy before authorizing access.

By default, Aembit retrieves the cluster’s public signing keys from its OpenID Connect (OIDC) discovery endpoints, so you supply the cluster’s issuer URL. For a cluster whose issuer isn’t reachable from the internet, upload the key material instead. See [Configuration](#configuration).

## How the Kubernetes Service Account Trust Provider works

Aembit resolves the cluster’s signing key from the OIDC endpoints you configure. During authentication, your workload, Aembit Edge, and Aembit Cloud perform a clear sequence of actions.

1. Aembit Edge reads the Service Account token that Kubernetes projected into the Client Workload’s pod and presents it to Aembit Cloud to prove the pod’s identity.

2. Aembit Cloud validates the token’s signature against the cluster’s public keys and confirms the token hasn’t expired. Aembit caches the keys and fetches them from the configured OIDC endpoints only when it sees a signing key it doesn’t recognize.

3. If the signature is valid, Aembit compares the token’s claims against the match rules you configured, including the issuer, subject, namespace, pod name, and Service Account name.

4. If the signature and all claims are valid, Aembit authorizes the request and applies the relevant Access Policies. If any check fails, Aembit denies the request.

![Aembit Edge presents the pod's token, and Aembit Cloud resolves the signing key from the configured OIDC endpoints, validates the token, and evaluates the Access Policy](https://docs.aembit.io/d2/docs/user-guide/access-policies/trust-providers/kubernetes-service-account-trust-provider-0.svg)

## Match rules

You authorize access by matching on the claims in the Service Account token. Each match rule pairs an **Attribute** with the **Value** you expect that claim to have. The following table describes the attributes available for the Kubernetes Service Account Trust Provider:

| Attribute                                   | Description                                                                                                                                                               |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `iss`                                       | Identifies the cluster that **issued** the token. Match on it to restrict the Trust Provider to one cluster. *Example*: `https://kubernetes.default.svc.cluster.local`    |
| `kubernetes.io { namespace }`               | The **namespace** the pod runs in. *Example*: `default`                                                                                                                   |
| `kubernetes.io { pod { name } }`            | The **name of the pod** that holds the token. Pods that a Deployment manages carry a generated suffix, so match on the prefix with a wildcard. *Example*: `example-app-*` |
| `kubernetes.io { serviceaccount { name } }` | The **Service Account** the pod runs as. *Example*: `example-app`                                                                                                         |
| `sub`                                       | The token **subject**, which combines the namespace and Service Account name. *Example*: `system:serviceaccount:default:example-app`                                      |

Values are case-sensitive, and `*` matches any sequence of characters. When you add more than one rule for the same attribute, a token passes if it matches any one of them. When you add rules for different attributes, a token must match every attribute.

Aembit requires at least one match rule; add a namespace or Service Account rule at minimum.

## Configuration

Select **Kubernetes Service Account** from the Trust Provider list, then add a match rule for each claim you want to verify. To add and associate the Trust Provider with an Access Policy, see [How to add a Trust Provider](add-trust-provider.md).

Under **Attestation Method**, leave **Method** set to **OIDC Discovery**. Paste each cluster’s OIDC issuer URL into an **OIDC Endpoint** field, and click **Add an Endpoint** to add a field for each additional cluster. The fields read **OIDC Endpoint 1**, **OIDC Endpoint 2**, and so on. Aembit Cloud must be able to reach each URL. If a cluster’s issuer isn’t reachable from the internet, choose **Upload JWKS** or **Upload Public Key** instead and supply the cluster’s public key material directly.

To attest one Client Workload that runs active-active across clusters, see [Attest one workload across multiple clusters](#attest-one-workload-across-multiple-clusters).

The form also shows the Trust Provider’s **Edge SDK Client ID**. Aembit Edge deployments that authenticate with a Service Account token, such as the [Aembit Secrets Operator](../../deploy-install/kubernetes/aso/overview.md), need this value. See [Find your Edge SDK Client ID](get-edge-sdk-client-id.md).

To attest an Agent Controller with this Trust Provider, select it when you [create the Agent Controller](../../deploy-install/advanced-options/agent-controller/create-agent-controller.md).

### Attest one workload across multiple clusters

A Client Workload that runs active-active across two or more clusters presents a different Service Account token in each cluster, each signed by that cluster’s key. Add one **OIDC Endpoint** for each cluster’s issuer URL, and the Trust Provider validates a token signed by any of them. One Trust Provider and one Access Policy then cover the Client Workload wherever it runs.

The `iss` claim identifies the cluster that issued the token, so a single `iss` match rule pins the Trust Provider to that one cluster. To span clusters, omit `iss`, or add one `iss` match rule per cluster so a token passes when it matches any of them.

### Find your cluster’s OIDC issuer URL

Every Kubernetes cluster publishes its issuer URL in its OIDC discovery document. Repeat this lookup for each cluster whose Client Workloads you attest, and add an **OIDC Endpoint** for each. With `kubectl` authenticated to the cluster, run:

**Any cluster**

```shell
kubectl get --raw /.well-known/openid-configuration
```

Copy the value of `issuer` from the JSON response. For details on the discovery document, see [Service account issuer discovery](https://kubernetes.io/docs/reference/access-authn-authz/service-accounts-admin/#service-account-issuer-discovery) in the Kubernetes documentation.

Managed clusters also expose the issuer URL through their cloud provider’s CLI. Install, configure, and authenticate your cloud provider’s CLI, then run its command below and paste each cluster’s output into its own **OIDC Endpoint** field.

**AWS CLI (EKS)**

```shell
aws eks describe-cluster --name <cluster_name> --query "cluster.identity.oidc.issuer" --output text
```

**Google Cloud CLI (GKE)**

```shell
gcloud container clusters describe <cluster_name> --region=<cluster_region> --format="value(selfLink)"
```

**Azure CLI (AKS)**

```shell
az aks show --resource-group <resource_group_name> --name <cluster_name> --query "oidcIssuerProfile.issuerUrl" -o tsv
```

### Terraform

You can manage this Trust Provider with the Aembit Terraform provider using the `aembit_trust_provider` resource. For the resource schema and arguments, see the [Aembit provider documentation](https://registry.terraform.io/providers/Aembit/aembit/latest/docs) in the Terraform Registry.

> **Set one endpoint argument**
>
> Set either `oidc_endpoint` or `oidc_endpoints` on a Trust Provider, not both. Use `oidc_endpoints` to configure one or more endpoints.

## Limitations

* **Per-cluster attribution.** An Access Authorization Event doesn’t record which cluster issued the token when one Trust Provider spans multiple clusters. The event confirms that attestation succeeded but doesn’t identify the endpoint that validated the token.

## Troubleshooting

If Aembit can’t validate a token’s signature, it denies the request rather than allow access it can’t verify.

If Aembit denies a request, check the following:

1. Open the Access Authorization Event for the request. The Trust Provider entry includes a `reason` that explains why attestation failed. See [Authorization Failure](../../audit-report/access-authorization-events.md#authorization-failure) for the values.

2. If the reason is an invalid signature, confirm an **OIDC Endpoint** matches the `issuer` the token’s cluster publishes and that Aembit Cloud can reach it.

3. If a match rule failed, compare the expected value in the rule with the actual value the event recorded. Pods that a Deployment manages get a new generated suffix on every rollout, so a rule on `kubernetes.io { pod { name } }` needs a wildcard.

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
