---
type: how-to
title: "Trusting certificates issued by private CAs"
description: "How to configure Aembit Edge Components to trust certificates issued by private CAs"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/trusting-private-cas/
interface: web-ui
tags: [advanced-option, deploy-install]
timestamp: 2025-08-22T15:04:10-07:00
type_inferred: true
---

# Trusting certificates issued by private CAs

Some Server Workloads use certificates issued by private Certificate Authorities (CAs), which aren’t publicly trusted. Agent Proxy, by default, doesn’t trust certificates issued by such private CAs and won’t connect to these workloads.

This article describes the steps required to configure Edge Components to establish trust with these certificate authorities.

## Add a private CA to an environment

[Section titled “Add a private CA to an environment”](#add-a-private-ca-to-an-environment)

The following sections describe how to add a private CA in different environments:

* [Kubernetes](#kubernetes)
* [AWS ECS](#aws-ecs)
* [Virtual machine](#virtual-machines)

### Kubernetes

[Section titled “Kubernetes”](#kubernetes)

To have your private CAs trusted, pass them as the `agentProxy.trustedCertificates` parameter in the Aembit Helm chart. This parameter should be a base64-encoded list of PEM-encoded certificates.

The resulting Helm command looks like this (remember to replace your tenant ID and other parameters):

```shell
helm install aembit aembit/aembit \
  --create-namespace -n aembit \
  --set agentProxy.trustedCertificates=LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0...
```

#### Volume-mounted certificates

[Section titled “Volume-mounted certificates”](#volume-mounted-certificates)

If your Kubernetes deployment disallows privilege escalation or requires a read-only filesystem, you need to include all trusted certificates through a volume.

Note

This method overrides the base image’s trusted CA certificates. Use this if you want explicit control of all trusted CA certificates within the Agent Proxy container.

To include trusted certificates as a volume, follow these steps:

1. Define a ConfigMap with the key `ca-certificates.crt`. Complete this step before deploying either the Aembit Helm chart or your Client Workload Pod.

   \<certs>.yaml

   ```yaml
   apiVersion: v1
   kind: ConfigMap
   metadata:
     name: <certs>
   data:
     # Certificates should be PEM-encoded
     ca-certificates.crt: |
       -----BEGIN CERTIFICATE-----
       MIIFmzCCBSGgAwIBAgIQCtiTuvposLf7ekBPBuyvmjAKBggqhkjOPQQDAzBZMQsw
       ...
   ```

2. Deploy your ConfigMap.

   ```shell
   kubectl -n <namespace> apply -f <certs.yaml>
   ```

3. Amend your Client Workload pod specification to provide your ConfigMap as a volume but do *not* deploy your Client Workload pod yet.

   ```yaml
   spec:
     volumes:
       - name: <cert-volume>
         configMap:
           name: <certs>
   ```

4. Deploy the Aembit Helm chart. Provide the name of your volume with the `agentProxy.trustedCertificatesVolumeName` parameter.

   ```shell
   helm install aembit aembit/aembit \
     --set agentProxy.trustedCertificatesVolumeName=<cert-volume>
   ```

5. Deploy your Client Workload Pod.

   ```shell
   kubectl -n <namespace> apply -f <client-workload-pod.yaml>
   ```

### AWS ECS

[Section titled “AWS ECS”](#aws-ecs)

To trust private CAs in AWS Elastic Container Service (ECS), pass them as a variable to the Aembit ECS Terraform module. This variable should be a Base64-encoded list of PEM-encoded certificates.

```hcl
module "aembit-ecs" {
  source  = "Aembit/ecs/aembit"
  version = "1.12.0"


  ...


  aembit_trusted_ca_certs = "LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0...."
}
```

### Virtual machines

[Section titled “Virtual machines”](#virtual-machines)

Agent Proxy automatically trusts all certificates installed in the host system’s trust root certificate store. The following steps are you add them to the appropriate system trust root certificate store.

#### Debian or Ubuntu-based VM

[Section titled “Debian or Ubuntu-based VM”](#debian-or-ubuntu-based-vm)

Place your private CA certificate in `/usr/local/share/ca-certificates/`, ensuring the file contains PEM-encoded certificates and that the file extension is `.crt`. Then, execute the following commands:

```shell
sudo apt-get update && sudo apt-get install -y ca-certificates
sudo update-ca-certificates
```

## Disable TLS verification

[Section titled “Disable TLS verification”](#disable-tls-verification)

In rare circumstances, Server Workloads could use certificates that full TLS verification would normally reject. For example, a Server Workload may have a certificate with a mismatch between the service’s FQDN and its CN or Subject Alternative Name (SAN).

Aembit allows you to turn off TLS verification for specific Server Workloads.

Caution

You must **exercise extreme caution** with this configuration.

Using certificates that full TLS verification rejects and turning off TLS verification represent poor security practices.

1. In your Aembit Tenant, go to **Server Workloads** in the left sidebar.

2. Select the Server Workload you want to configure.

3. Find the **Forward TLS Verification** dropdown menu and select **None**.

   ![Forward TLS Verification](https://docs.aembit.io/_astro/forward_tls_verification.BYORzZrG_plhRT.webp)
