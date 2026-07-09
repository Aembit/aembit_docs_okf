---
type: how-to
title: "Configure TLS Decrypt"
description: "How to configure TLS Decrypt when using HTTPS or Redis over TLS"
resource: https://docs.aembit.io/user-guide/deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt/
interface: web-ui
tags: [tls-decrypt, advanced-option, deploy-install]
timestamp: 2025-05-29T11:26:12-07:00
type_inferred: true
---

# Configure TLS Decrypt

When your Client Workload uses Transport Layer Security (TLS) (such as HTTPS or Redis with TLS) to communicate with the Server Workload, you must enable [TLS Decrypt](overview.md) in your Aembit Tenant. TLS Decrypt allows the Aembit Agent Proxy to decrypt and manage encrypted traffic between your Client and Server Workloads, enabling Workload IAM functionality.

To configure TLS Decrypt, you must configure your Client Workloads to trust your Aembit Tenant Root Certificate Authorities (CAs) so they can establish TLS connections with your Server Workload. To do this, you must:

* [Get your Aembit Tenant Root CA](#get-your-aembit-tenant-root-ca).

* [Add the root CA to the root store](#add-your-aembit-tenant-root-ca-to-a-trusted-root-store) on your Client Workloads.

* You also have the option to [change your Leaf Certificate Lifetime](#change-your-leaf-certificate-lifetime) (default 1 day).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

To configure TLS Decrypt, you must have the following:

* A Server Workload with TLS enabled (see [Enable Server Workload TLS](../../../access-policies/server-workloads/server-workload-enable-tls.md)).

* Your Aembit Tenant Root CA.

* TLS version 1.2+ on your Client and Server Workloads (Agent Proxy requirement).

  Note

  If your Client Workloads support TLS version 1.3, then Agent Proxy uses TLS version 1.3.

## Get your Aembit Tenant Root CA

[Section titled “Get your Aembit Tenant Root CA”](#get-your-aembit-tenant-root-ca)

To get your Aembit Tenant Root CA, perform the following steps:

1. Log in to your Aembit Tenant.

2. In the left sidebar menu, go to **Edge Components**.

3. In the top ribbon menu, click **TLS Decrypt**.

   ![TLS Decrypt Page](https://docs.aembit.io/_astro/tls_decrypt.C32a0KWO_1kPyNr.webp)

4. Click Download your Aembit Tenant Root CA certificate.

Alternatively, you may download the Aembit Tenant Root CA directly by using to the following URL, making sure to replace `<your_tenant_id>` with your actual Aembit Tenant ID:

```shell
https://<your_tenant_id>.aembit.io/api/v1/root-ca
```

## Add your Aembit Tenant Root CA to a trusted root store

[Section titled “Add your Aembit Tenant Root CA to a trusted root store”](#add-your-aembit-tenant-root-ca-to-a-trusted-root-store)

Different operating systems and application frameworks have different methods for adding root certificates to their associated root store. Most Client Workloads use the system root store. This isn’t always the case, however, so make sure to consult your operating system’s documentation.

You must install your Aembit Tenant Root CA on your Client Workload container or Virtual Machine (VM). Install your Aembit Tenant Root CA either during workload build/provisioning time, or at runtime, as long as the Client Workload processes trust the Aembit Tenant Root CA.

Select a tab for your operating system, distribution, and specific application to see the steps to adding your Aembit Tenant Root CA to your root store:

* Debian/Ubuntu-based container

  For Debian/Ubuntu Linux, you must include the Aembit Tenant Root CA in your Client Workload container image:

  1. [Get your Aembit Tenant Root CA](#get-your-aembit-tenant-root-ca) and save it to `</your/file/path>/<aembit-tenant-root-ca>.crt`.

  2. Run the following commands to include the root CA in your `Dockerfile`:

     ```dockerfile
     RUN apt-get update && apt-get install -y ca-certificates
     COPY </your/file/path>/<aembit-tenant-root-ca>.crt /usr/local/share/ca-certificates
     RUN update-ca-certificates
     ```

* Debian/Ubuntu-based VM

  ```shell
  sudo apt-get update && sudo apt-get install -y ca-certificates
  sudo wget https://<your_tenant_id>.aembit.io/api/v1/root-ca \
    -O /usr/local/share/ca-certificates/<aembit-tenant-root-ca>.crt
  sudo update-ca-certificates
  ```

* Red Hat VM

  ```shell
  sudo yum update -y && sudo yum install -y ca-certificates
  sudo wget https://<your_tenant_id>.aembit.io/api/v1/root-ca \
    -O /etc/pki/ca-trust/source/anchors/<aembit-tenant-root-ca>.crt
  sudo update-ca-trust
  ```

* Windows Server VM

  ```powershell
  Invoke-WebRequest `
    -Uri https://<your_tenant_id>.aembit.io/api/v1/root-ca `
    -Outfile <aembit-tenant-root-ca>.cer
  Import-Certificate `
    -FilePath <aembit-tenant-root-ca>.cer `
    -CertStoreLocation Cert:\LocalMachine\Root
  ```

* Node.js-based Client Workload

  Node.js uses its own certificate store, distinct from the system’s certificate store (such as `/etc/ssl/certs/ca-certificates.crt` on Ubuntu/Debian and `/etc/pki/tls/certs/` on RedHat), to manage and validate trusted root CAs.

  To include additional trusted root certificates, use the environment variable [NODE\_EXTRA\_CA\_CERTS](https://nodejs.org/api/cli.html#node_extra_ca_certsfile):

  1. [Get your Aembit Tenant Root CA](#get-your-aembit-tenant-root-ca).

  2. Set the `NODE_EXTRA_CA_CERTS` environment variable accordingly.

* Python-based Client Workload

  For Python-based applications, [get your Aembit Tenant Root CA](#get-your-aembit-tenant-root-ca), then follow the section that applies to you:

  #### Using the Python `requests` library

  [Section titled “Using the Python requests library”](#using-the-python-requests-library)

  Configure the environment variable `REQUESTS_CA_BUNDLE` to point to a bundle of trusted certificates, including the Aembit Tenant Root CA.

  For more details, refer to the [requests advanced user guide](https://requests.readthedocs.io/en/latest/user/advanced/).

  #### Using the Python `httpx` package

  [Section titled “Using the Python httpx package”](#using-the-python-httpx-package)

  Configure the environment variable `SSL_CERT_FILE` to include the Aembit Tenant Root CA.

  For additional information, see [PEP 476](https://peps.python.org/pep-0476/).

* Other

  Please contact Aembit support if you need instructions for a different distribution or trust root store location.

## Change your leaf certificate lifetime

[Section titled “Change your leaf certificate lifetime”](#change-your-leaf-certificate-lifetime)

The default lifetime of leaf certificates for your Aembit Tenant Root CA is **1 day**. To change this value, follow these steps:

1. Log in to your Aembit Tenant.

2. In the left sidebar menu, go to **Edge Components**.

3. In the top ribbon menu, click **TLS Decrypt**.

4. Under **Leaf Certificate Lifetime**, select the desired value (`1 hour`, `1 day`, or `1 week`) from the dropdown menu.

5. Click **Save**.

6. (Optional) To apply the changes to existing leaf certificates, you must either:

   * Restart the associated Agent Proxy. See [Verifying your leaf certificate lifetime](#verifying-your-leaf-certificate-lifetime).

   * Wait for existing certificates to expire.

     Security best practice

     Changing the lifetime duration for leaf certificates doesn’t require reinstallation of the root CA certificate in any location where it’s already installed. The root CA certificate itself remains unchanged, and this modification only affects the validity period of newly issued leaf certificates.

     That said, it’s important to remember that **existing certificates retain their original expiration dates**. This means you’ll need to restart the associated Agent Proxy to fully transition to the shorter lifetime. This is especially important for more drastic decreases like going from one week to one hour.

### Verifying your leaf certificate lifetime

[Section titled “Verifying your leaf certificate lifetime”](#verifying-your-leaf-certificate-lifetime)

[After changing your leaf certificate lifetime](#change-your-leaf-certificate-lifetime), verify the changes by viewing the details of the cert through the following commands:

1. After changing the leaf certificate lifetime, log in to the Agent Proxy associated with the leaf certificate lifetime you updated.

2. Restart the Agent Proxy.

3. Run the following command to create a test TLS connection from the Agent Proxy to a Server Workload. The hostname must be in a Server Workload associated with the Access Policy for that Agent Proxy.

   ```shell
   openssl s_client -connect <server_workload_hostname>:<port>
   ```

4. Inspect the output and look for the `Server certificate` section.

   Copy the contents of the certificate (highlighted in the following example):

   ```txt
   Server certificate
   -----BEGIN CERTIFICATE-----
   MjUwMjA1MjI1MDIxWhcNMzUwMjAzMjI1MDIxWjBrMSUwIwYDVQQDDBxBZW1iaXQg
   ...
   ... omitted for brevity
   ...
   0ApHb7jB+YkL59eG9WOdCUqjQjBAA=
   -----END CERTIFICATE-----
   subject-CN - my.service.com
   ```

5. View and inspect the detailed contents of the certificate by echoing the certificate you just copied into the `openssl x509 -text` command:

   ```shell
   echo "<copied_server_certificate>" | openssl x509 -text
   ```

   You should see output similar to the following:

   ```shell
   Certificate:
       Data:
           Version: 3 (0x2)
           Serial Number: 1234567890 (0x12345fe4)
           Signature Algorithm: ecdsa-with-SHA384
           Issuer: CN = Aembit Tenant 1a2b3c Issuing CA, O = Aembit Inc, C = US, emailAddress = support@aembit.io
           Validity
               Not Before: Feb 10 13:25:42 2025 GMT
               Not After : Feb 11 13:30:42 2025 GMT
           Subject: CN = my.service.com
              ...
              ... omitted for brevity
              ...
   ```

   Notice that the highlighted `Validity` section has the new lifetime representing the leaf certificate lifetime you selected. Aembit intentionally adds five minutes to the `Not Before` time to account for clock skew between different systems.
