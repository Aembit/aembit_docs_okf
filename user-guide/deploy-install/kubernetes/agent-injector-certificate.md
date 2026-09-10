---
type: how-to
title: "Managing the Agent Injector TLS certificate"
description: "How to configure the TLS certificate used by Aembit Agent Injector."
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/agent-injector-certificate/
interface: web-ui
tags: ["kubernetes", "deploy-install"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Managing the Agent Injector TLS certificate

Your Aembit Edge deployment on Kubernetes includes three components:

* Agent Proxy
* Agent Controller
* Agent Injector

Agent Proxy and Agent Controller require little maintenance. Agent Injector, however, relies on a TLS certificate that you must keep up to date.

Agent Injector mutates your Client Workload PodSpec to inject Agent Proxy, which enables your Client Workload Pod to connect to the Server Workload Pod. Agent Injector’s TLS certificate secures communication with the Kubernetes API Server. If the certificate is invalid, the API Server blocks the mutation and the Agent Proxy isn’t injected.

This document explains how to manage the Agent Injector TLS certificate to avoid disruption to the Agent Proxy injection process.

## Agent Proxy container injection process

When you deploy a Client Workload Pod, the Agent Injector mutates your PodSpec to inject the Agent Proxy container definitions. The [Kubernetes admission control process](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/) orchestrates the injection. The following diagram shows the sequence of operations affecting your Client Workload PodSpec as it undergoes the admission control process.

The red animated line shows where the Agent Injector TLS certificate can disrupt the process.

![Agent proxy container injection process](https://docs.aembit.io/d2/docs/user-guide/deploy-install/kubernetes/agent-injector-certificate-0.svg)

The `MutatingWebhookConfiguration` tells your Kubernetes cluster how to reach the Agent Injector. If the cluster receives an unexpected TLS certificate from the Agent Injector the cluster won’t allow it to inject the Agent Proxy container definitions.

Continue reading to learn how to keep this communication working and manage the Agent Injector TLS certificate.

## Kubernetes resources related to the Agent Injector

The Agent Injector TLS certificate is a Kubernetes `Secret` resource. The `Secret` resource provides the TLS certificate and private key to the Agent Injector pod. It also provides the Certificate Authority certificate to the `MutatingWebhookConfiguration`. The injection process fails when these components disagree on which TLS certificate the Agent Injector is using.

The following diagram shows the Aembit Edge components and Kubernetes resources involved in the Agent Injector TLS certificate management:

![Agent Injector TLS Certificate Management](https://docs.aembit.io/d2/docs/user-guide/deploy-install/kubernetes/agent-injector-certificate-1.svg)

## Managing the Agent Injector TLS certificate

You have multiple options for how to manage this secret:

* [Generate a self-signed certificate with the Helm chart](#generate-a-self-signed-certificate-with-the-helm-chart)
* [Create a cert-manager Certificate resource](#create-a-cert-manager-certificate-resource)
* [Manually create a TLS Secret resource](#manually-create-a-tls-secret-resource)

### Generate a self-signed certificate with the Helm chart

The Aembit Helm chart generates a self-signed certificate by default. The Helm chart simultaneously configures the `MutatingWebhookConfiguration` to expect this self-signed certificate. In other contexts, a TLS configuration requires an independent Certificate Authority to provide the authenticity guarantee of TLS. In this context, the user or service account deploying the Helm chart configures both sides of the TLS connection. This symmetric configuration provides the authenticity guarantee of TLS.

The self-signed certificate presents two challenges:

1. You must re-apply the Aembit Helm chart to generate a new self-signed certificate before the certificate expires. The certificate is valid for one year.

   > **Tip**
   >
   > Aembit recommends adding this to your certificate rotation management schedule.

2. The Aembit Helm Chart generates a new self-signed certificate each time it’s applied. When used with ArgoCD’s automatic synchronization feature, the dynamic nature of the certificate causes the ArgoCD diff detection to consider the Agent Injector configuration out-of-sync as soon as the synchronization completes.

   See the [ArgoCD Diffing Customization guide](https://argo-cd.readthedocs.io/en/stable/user-guide/diffing/) for guidance to squelch these differences.

   > **Note**
   >
   > Exempting the self-signed certificate requires you to manually re-apply the Aembit Helm Chart **once per year.**

### Create a cert-manager Certificate resource

This is likely your best option if you already use cert-manager to manage other certificates within your cluster. Using a cert-manager certificate with the Aembit Helm chart is straightforward.

To configure the Agent Injector to use a cert-manager `Certificate` resource, follow these steps:

1. Create your namespace.

   ```shell
   kubectl create namespace <your-namespace>
   ```

2. Create the `Certificate` resource within the namespace you plan to deploy the Aembit Edge Components. Take note of the `secretName` value you provide at this step.

   Click to reveal an example

   The following example creates a `Certificate` resource that uses the `letsencrypt-prod` ClusterIssuer to issue a TLS certificate for the domain `edge.your-domain.com`. Adjust the `dnsNames` and `issuerRef` values to match your environment.

   ```yaml
   apiVersion: cert-manager.io/v1
   kind: Certificate
   metadata:
     name: aembit-edge-tls # The name of this Certificate resource
     namespace: your-namespace # The namespace you just created
   spec:
     # The name of the Secret to create to store the certificate
     secretName: aembit-edge-tls-secret


     # The domain name for your Aembit Edge instance
     dnsNames:
       - edge.your-domain.com


     # Reference to the Issuer or ClusterIssuer that will sign the certificate
     issuerRef:
       name: letsencrypt-prod # Or your preferred issuer
       kind: ClusterIssuer
   ```

   This is just an example, so make sure to adjust these values to match your environment.

3. Verify that the `Certificate` is approved and marked as `Ready` for use.

   ```shell
   kubectl -n <namespace> get certificates


   NAME              READY   SECRET            ISSUER              STATUS                                         AGE
   my-app-tls        True    my-app-tls       letsencrypt-prod    Certificate is up to date and has not expired  95d
   api-service-tls   True    api-service-tls  letsencrypt-prod    Certificate is up to date and has not expired  32d
   ```

4. Deploy the Aembit Helm Chart, providing these additional values:

   ```shell
   helm install aembit aembit/aembit \
       -n <namespace> \
       --create-namespace \
       --set tenant=<tenantId> \
       --set agentController.id=<agentControllerId> \
       --set 'agentInjector.webhookAnnotations.cert-manager\.io/inject-ca-from=<namespace>\/<certificate name>' \
       --set agentInjector.certificate.create=false \
       --set agentInjector.certificate.commonName=<certificate secret name>\
   ```

   > **Note**
   >
   > Make sure to leave the backslashes in `cert-manager\.io` and `<namespace>\/<certificate name>`, as they’re important.

5. Verify the certificate configuration of the `MutatingWebhookConfiguration`.

   ```shell
   kubectl get mutatingwebhookconfiguration aembit-agent-injector.<namespace>.aembit.io \
       -o jsonpath='{$.webhooks[0].clientConfig.caBundle}' \
       | openssl enc -d -base64 -A \
       | openssl x509 -noout -subject


   subject=CN=<your cluster CA>
   ```

   If you don’t see the expected certificate authority check your cert-manager ca-injector logs.

6. Verify the certificate used by the Agent Injector:

   ```shell
   kubectl -n <namespace> get pod -l aembit.io/component=aembit-agent-injector \
       -o jsonpath='{$.items[0].spec.volumes[0].secret.secretName}'
   ```

   Double check that this outputs the same value as `<certificate secret name>` you used in the previous steps.

   ```shell
   kubectl -n <namespace> get secret <certificate secret name> \
       -o jsonpath="{\$.data['ca\.crt']}" \
       | openssl enc -d -base64 -A \
       | openssl x509 -noout -issuer
   ```

Now that you’ve configured Agent Injector to use a `Certificate` resource that is issued by your cluster’s cert-manager installation, the certificate renewal should occur on the same schedule as other certificates within your cluster.

### Manually create a TLS Secret resource

Using a manually created TLS `Secret` resource is also straight forward. It works similar to the

1. Create a TLS `Secret` resource with the private key, certificate, and CA certificate.

2. Retrieve the CA certificate from the `Secret` resource:

   ```shell
   oc -n <namespace> get secret <secret name> -o jsonpath="{\$.data['ca\.crt']}"


   LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0....
   ```

3. Deploy the Aembit Helm Chart, disabling the automatic certificate creation with `agentInjector.certificate.create=false` and providing the CA certificate in the `agentInjector.certificate.caBundle` value:

   ```shell
   helm install aembit aembit/aembit \
       -n <namespace> \
       --create-namespace \
       --set tenant=<tenantId> \
       --set agentController.id=<agentControllerId> \
       --set agentInjector.certificate.create=false \
       --set agentInjector.certificate.commonName=<certificate secret name>\
       --set agentInjector.certificate.caBundle=<certificate>
   ```

## Troubleshooting the Agent Injector TLS certificate

When your cluster receives an unexpected TLS certificate from the Agent Injector, the cluster drops the connection and, in effect, refuses to inject the Agent Proxy container definitions. Without credential injection Server Workloads will either reject requests from Client Workloads or provide unexpected responses.

To determine whether an unexpected certificate is preventing Agent Proxy container injections tail the logs of the Kubernetes `kube-apiserver` component. Then deploy your Client Workload Pod. Look for errors similar to:

```shell
"Unhandled Error" err="failed calling webhook \"aembit-agent-injector.cm-demo.aembit.io\": failed to call webhook: Post \"https://aembit-agent-injector.cm-demo.svc:443/mutate?timeout=10s\": tls: failed to verify certificate: x509: certificate is not valid for any names, but wanted to match aembit-agent-injector.cm-demo.svc" logger="UnhandledError"
```

The Kubernetes distribution you use determines where the `api-server` logs are available and how you can access them. On a cluster following baseline Kubernetes conventions:

```shell
kubectl -n kube-system logs -l component=kube-apiserver --all-pods -c kube-apiserver -f
```

On OpenShift clusters:

```shell
oc -n openshift-kube-apiserver logs -l app=openshift-kube-apiserver --all-pods -c kube-apiserver -f
```

On EKS clusters, look in `CloudWatch` for your `apiserver` logs. Consult the EKS documentation for details regarding the log groups:

* [Send control plane logs to CloudWatch Logs](https://docs.aws.amazon.com/eks/latest/userguide/control-plane-logs.html)
* [Logging for Amazon EKS](https://docs.aws.amazon.com/prescriptive-guidance/latest/implementing-logging-monitoring-cloudwatch/kubernetes-eks-logging.html)
