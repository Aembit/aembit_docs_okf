---
type: how-to
title: "Verify the Aembit Edge Helm chart signature"
description: "How to verify the Aembit Edge Helm chart signature"
resource: https://docs.aembit.io/user-guide/deploy-install/kubernetes/verify-helm-chart/
interface: web-ui
tags: [kubernetes, deploy-install]
timestamp: 2026-06-26T13:11:59-07:00
type_inferred: true
---

# Verify the Aembit Edge Helm chart signature

Aembit provides a Helm chart that simplifies the deployment of Aembit Edge Components in your Kubernetes cluster. As a best practice, you should verify the Helm chart before deploying it to verify its integrity and authenticity.

This page describes how to verify the Aembit Helm chart you’ll use in your Kubernetes cluster. You can verify the Helm chart using the following methods:

* [Helm CLI](#verify-using-the-helm-cli)
* [Terraform](#verify-using-terraform)
* [manually](#verify-manually)

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To verify the Aembit Edge Helm chart, you must have the following:

* [`kubctl` installed](https://kubernetes.io/docs/tasks/tools/#kubectl)

* [`helm` installed](https://helm.sh/docs/intro/install/)

* (Optional) `gpg` (GNU Privacy Guard) installed

  Expand to install `gpg`

  To install `gpg`, select a tab for your OS and follow the instructions:

  * Linux/WSL

    Debian:

    ```shell
    apt install gnupg
    ```

    RHEL:

    ```shell
    yum install gnupg2
    ```

  * MacOS

    ```shell
    brew install gnupg
    ```

  * Windows

    PowerShell:

    ```powershell
    winget install GnuPG.GnuPG
    ```

    Chocolatey:

    ```powershell
    choco install gpg4win
    ```

    Or, if you’re using WSL, follow the Linux/WSL tab’s instructions.

* A Kubernetes cluster that’s running and accessible from your local machine

* Your Kubernetes context set to the cluster where you want to deploy Aembit Edge Components

  Expand to verify and set Kubernetes context

  To verify that you have set your current context in Kubernetes correctly:

  ```shell
  kubectl config current-context
  ```

  If the context output is incorrect, set it correctly by running:

  ```shell
  kubectl config use-context <your-cluster-context>
  ```

## Verify using the Helm CLI

[Section titled “Verify using the Helm CLI”](#verify-using-the-helm-cli)

The following steps describe how to verify the Aembit Edge Helm chart using Helm with signature verification. This method provides explicit verification of the chart’s signature and ensures that the chart is valid before installation.

1. Add or update the Aembit Helm repository to your local Helm configuration by running:

   ```shell
   # Add the Aembit Helm repository
   helm repo add aembit https://helm.aembit.io


   # Update the Helm repository to ensure you have the latest charts
   helm repo update aembit
   ```

2. Import the Aembit Edge Helm PGP public keys from [Aembit’s Keybase repository](https://keybase.io/aembit) into your GPG keyring:

   ```shell
   curl "https://keybase.io/aembit/pgp_keys.asc" | gpg --import
   ```

3. Export your GPG keyring to a format compatible with Helm:

   ```shell
   gpg --export --output ~/.gnupg/pubring.gpg
   ```

   Why export the public key?

   You must export your keyring because Helm requires the older GPG keyring format (`.gpg`) and can’t use the newer keyring format (`.kbx`) that modern GPG uses by default.

4. Choose your verification method:

   * Verify Dry-run

     ```shell
     helm install aembit aembit/aembit \
       --verify \
       --keyring ~/.gnupg/pubring.gpg \
       --dry-run \
       --set tenant=<tenantId>,agentController.id=<agentControllerId>
     ```

   * Verify Install

     ```shell
     helm install aembit aembit/aembit \
       --verify \
       --keyring ~/.gnupg/pubring.gpg \
       --set tenant=<tenantId>,agentController.id=<agentControllerId>
     ```

5. Review the output:

   * Verify Dry-run

     When using `--verify` with `--dry-run`, successful verification produces no output. You’ll see the following dry-run output if the verification is successful:

     ```shell
     NAME: aembit
     LAST DEPLOYED: Wed Jul  9 12:54:13 2025
     NAMESPACE: default
     STATUS: pending-install
     REVISION: 1
     TEST SUITE: None
     HOOKS:
     MANIFEST:
     ---
     # Source: aembit/templates/serviceaccount.yaml
     # [YAML output continues...]
     ```

   * Verify Install

     When using `--verify` with actual installation, successful verification produces no output. You’ll see the following installation output if the verification is successful:

     ```shell
     NAME: aembit
     LAST DEPLOYED: Wed Jul  9 12:54:13 2025
     NAMESPACE: default
     STATUS: deployed
     REVISION: 1
     TEST SUITE: None
     NOTES:
     # [Installation notes and instructions continue...]
     ```

   If verification fails for either method, you’ll see an error message like:

   ```shell
   Error: failed to verify chart signature
   ```

## Verify using Terraform

[Section titled “Verify using Terraform”](#verify-using-terraform)

You can also verify the Aembit Edge Helm chart using Terraform, ensuring that the installation occurs only if the chart is authentic and valid.

### Prerequisites

[Section titled “Prerequisites”](#prerequisites-1)

Complete steps 2-3 from the [Helm CLI section](#verify-using-the-helm-cli) to import the Aembit key and export your keyring.

### Terraform configuration

[Section titled “Terraform configuration”](#terraform-configuration)

You must add the following options to your Terraform configuration to enable verification of the Helm chart signature:

* `verify` enables the verification process
* `keyring` specifies the path to the GPG keyring that contains the public key used to sign the Helm chart

```hcl
provider "helm" {
  kubernetes {
    config_path = "~/.kube/config"
  }


  verify = true # Enable verification of the Helm chart signature
  keyring = "~/.gnupg/pubring.gpg" # Path to the GPG keyring
}


resource "helm_release" "aembit_edge" {
  name       = "aembit"
  repository = "https://helm.aembit.io"
  chart      = "aembit"


  set {
    name  = "tenant"
    value = var.tenant_id
  }


  set {
    name  = "agentController.id"
    value = var.agent_controller_id
  }
}
```

If the verification is successful, you’ll get output indicating that the plan was successful and that Terraform won’t make any changes to your cluster. If there are any issues with the Helm chart or its signature, Terraform reports an error.

## Verify manually

[Section titled “Verify manually”](#verify-manually)

To manually verify the signature of the Aembit Edge Helm chart, follow these steps:

1. Move to a directory you want to save the Helm chart package in, for example:

   ```shell
   mkdir aembit-helmChart && cd aembit-helmChart
   ```

2. Add or update the Aembit Helm repository to your local Helm configuration by running:

   ```shell
   # Add the Aembit Helm repository
   helm repo add aembit https://helm.aembit.io


   # Update the Helm repository to ensure you have the latest charts
   helm repo update aembit
   ```

3. Download the Aembit Edge Helm chart package and its signature from the Aembit Helm repository. Replace `<helmChartVersion>` with the version of the Helm chart you want to download:

   ```shell
   wget https://helm.aembit.io/aembit-<helmChartVersion>.tgz
   wget https://helm.aembit.io/aembit-<helmChartVersion>.tgz.prov
   ```

4. Verify the chart signature using the following command:

   ```shell
   helm verify aembit-<helmChartVersion>.tgz
   ```

   Replace `<helmChartVersion>` with the actual version of the chart you downloaded.

5. If the verification is successful, you’ll get the following output:

   ```shell
   Signed by: Aembit, Inc. <keybase@aembit.io>
   Using Key With Fingerprint: EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
   Chart Hash Verified: sha256:48db111f899405e219d3f8cc05abed644cfa10617c558fa5021be1def592c05c
   ```

   If there are any issues with the signature, you’ll receive an error message.

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

If you encounter issues during the verification process, here are some common errors and their solutions:

### Key Not Found

[Section titled “Key Not Found”](#key-not-found)

```shell
Error: keyring "~/.gnupg/pubring.gpg" does not exist
```

**Solution**: Ensure you’ve exported the keyring using step 3 in the Helm CLI section.

### Signature verification failed

[Section titled “Signature verification failed”](#signature-verification-failed)

```shell
Error: failed to verify chart signature
```

**Possible causes**:

* Wrong public key imported
* Chart wasn’t signed with expected key
* Corrupted download

Verify you have the correct key:

```shell
gpg --list-keys aembit
gpg --fingerprint EA3D8D2FDAC6BD8137163D00D655E64729BC67D7
```

### Permission issues

[Section titled “Permission issues”](#permission-issues)

```shell
Error: permission denied accessing keyring
```

**Solution**: Check file permissions on your GPG directory:

```shell
ls -l ~/.gnupg
```

Ensure your user has read access to the `pubring.gpg` file. If not, adjust permissions:

```shell
chmod 700 ~/.gnupg
chmod 600 ~/.gnupg/*
```

### Chart repository issues

[Section titled “Chart repository issues”](#chart-repository-issues)

If you get repository-related errors:

```shell
# Remove and re-add the repository
helm repo remove aembit
helm repo add aembit https://helm.aembit.io
helm repo update
```
