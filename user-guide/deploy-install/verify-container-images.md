---
type: how-to
title: "Verifying Aembit container image signatures"
description: "How to verify official Aembit container image signatures"
resource: https://docs.aembit.io/user-guide/deploy-install/verify-container-images/
interface: web-ui
tags: [deploy-install]
timestamp: 2025-07-10T08:39:16-07:00
type_inferred: true
---

# Verifying Aembit container image signatures

Aembit cryptographically signs all container images in [Aembit’s Docker Hub repositories](https://hub.docker.com/u/aembit). To verify container image signatures, Aembit suggests using [`cosign`](https://docs.sigstore.dev/cosign/verifying/verify/), a CLI utility for signing software artifacts and verifying signatures using [Sigstore](https://docs.sigstore.dev/).

Aembit signs all container images in Docker Hub starting from the following versions:

* [`aembit_agent_controller`](https://hub.docker.com/r/aembit/aembit_agent_controller) `v1.23.2263+`
* [`aembit_agent_proxy`](https://hub.docker.com/r/aembit/aembit_agent_proxy) `v1.23.3002+`
* [`aembit_agent_injector`](https://hub.docker.com/r/aembit/aembit_agent_injector) `v1.23.295+`
* [`aembit_aws_lambda_extension`](https://hub.docker.com/r/aembit/aembit_aws_lambda_extension) `v1.23.112+`
* [`aembit_sidecar_init`](https://hub.docker.com/r/aembit/aembit_sidecar_init) `v1.18.92+`

## Verify a container image tag

[Section titled “Verify a container image tag”](#verify-a-container-image-tag)

The following example shows how to verify the container image signature for Agent Controller. Though, you can swap the image name to any of the other available container images available in Aembit’s Docker Hub.

To verify the `aembit_agent_controller` container image:

1. Download the [Aembit Image Signing verification public key](https://docs.aembit.io/aembit-cosign-public-key.pub).

2. Install `cosign` using [Cosign's official installation guide](https://docs.sigstore.dev/cosign/system_config/installation/).

3. Run the following command to verify the signature for an image:\
   *The following command always uses the latest tag*.

   ```shell
   cosign verify --key <path-to-public-key> aembit/aembit_agent_controller:latest
   ```

   If successful, Cosign confirms the image signature and display the following verification details:

   ```shell
   [{
     "critical": {
       "identity": {
         "docker-reference": "index.docker.io/aembit/aembit_agent_controller"
       },
       "image": {
         "docker-manifest-digest": "sha256:528de2fadc98d0a ..."
       },
       "type": "cosign container image signature"
     },
     "optional": {
       "Bundle": {
         "SignedEntryTimestamp": "MEUCIQDUKU204hbQx ... vPA9+yrvC90uxFJ4=",
         "Payload": {
           "body": "eyJlvNmgvZTA5M1MzUjNpckxrTnhpYzNlUCtvPSIsInB1YmxpY0tleSI6eyJ ..."
   }}}}]
   ```

## Verify a specific container image tag

[Section titled “Verify a specific container image tag”](#verify-a-specific-container-image-tag)

Use the commands from the following sections to verify specific Docker Hub tags for Aembit container images. You can verify all images with the same public key.

**Public key**: [Aembit Image Signing verification public key](https://docs.aembit.io/aembit-cosign-public-key.pub)

The command to use `cosign` should look similar to the following example, where `<tag>` is the specific version that you want to verify the signature.

```shell
cosign verify --key <path-to-public-key> aembit/<image_name>:<tag>
```

### Agent Controller

[Section titled “Agent Controller”](#agent-controller)

**Image name**: `aembit_agent_controller`

**Docker Hub repo**: [`aembit/aembit_agent_controller`](https://hub.docker.com/r/aembit/aembit_agent_controller)

**Latest version**: `1.32.3502`

**Verification command**:

```shell
cosign verify --key <path-to-public-key> aembit/aembit_agent_controller:1.32.3502
```

### Agent Proxy

[Section titled “Agent Proxy”](#agent-proxy)

**Image name**: `aembit_agent_proxy`

**Docker Hub repo**: [`aembit/aembit_agent_proxy`](https://hub.docker.com/r/aembit/aembit_agent_proxy)

**Latest version**: `1.32.4999`

**Verification command**:

```shell
cosign verify --key <path-to-public-key> aembit/aembit_agent_proxy:1.32.4999
```

### Agent Injector

[Section titled “Agent Injector”](#agent-injector)

**Image name**: `aembit_agent_injector`

**Docker Hub repo**: [`aembit/aembit_agent_injector`](https://hub.docker.com/r/aembit/aembit_agent_injector)

**Latest version**: `1.31.425`

**Verification command**:

```shell
cosign verify --key <path-to-public-key> aembit/aembit_agent_injector:1.31.425
```

### AWS Lambda Extension

[Section titled “AWS Lambda Extension”](#aws-lambda-extension)

**Image name**: `aembit_aws_lambda_extension`

**Docker Hub repo**: [`aembit/aembit_aws_lambda_extension`](https://hub.docker.com/r/aembit/aembit_aws_lambda_extension)

**Latest version**: `1.32.168`

**Verification command**:

```shell
cosign verify --key <path-to-public-key> aembit/aembit_aws_lambda_extension:1.32.168
```

### Sidecar Init

[Section titled “Sidecar Init”](#sidecar-init)

**Image name**: `aembit_sidecar_init`

**Docker Hub repo**: [`aembit/aembit_sidecar_init`](https://hub.docker.com/r/aembit/aembit_sidecar_init)

**Latest version**: `1.25.130`

**Verification command**:

```shell
cosign verify --key <path-to-public-key> aembit/aembit_sidecar_init:1.25.130
```

## Verify a container image digest

[Section titled “Verify a container image digest”](#verify-a-container-image-digest)

To verify a specific container image digest, you can use the `cosign` command with the `sha256` digest of the image.

The command to use `cosign` should look similar to the following example, where `<hash variable>` is the specific digest of the image you want to verify.

```shell
cosign verify --key <path-to-public-key>  aembit/aembit_agent_controller@sha256:<hash variable>
```

Example successfully verified output:

```shell
cosign verify --key ./aembit-cosign-public-key.pub  aembit/aembit_agent_controller@sha256:528de2fadc98d0affea24bc03920ed531825779f3a8246f72bf2d568324f4daf


Verification for index.docker.io/aembit/aembit_agent_controller@sha256:528de2fadc98d0affea24bc03920ed531825779f3a8246f72bf2d568324f4daf --
The following checks were performed on each of these signatures:
  - The cosign claims were validated
  - Existence of the claims in the transparency log was verified offline
  - The signatures were verified against the specified public key


[{"critical":{"identity":{"docker-reference":"index.docker.io/aembit/aembit_agent_controller"},"image":{"docker-manifest-digest":"sha256:528de2fadc98d0affea24bc03920ed531825779f3a8246f72bf2d568324f4daf"},"type":"cosign container image signature"},"optional":{"Bundle":{"SignedEntryTimestamp":"MEUCIQDUKU204hbQxCwxvwz9iTiccDdf3dc8NE7lO12KQ2GlwQIgCNjs8XiwipX7x0uv0h9Mvz5r/GZrPA9+yrvC90uxFJ4=","Payload":{"body":"eyJhcGlWZXJzaW9uI...=","integratedTime":1750106188,"logIndex":240203120,"logID":"c0d23d6ad406973f9559f3ba2d1ca01f84147d8ffc5b8445c224f98b9591801d"}}}}]
```
