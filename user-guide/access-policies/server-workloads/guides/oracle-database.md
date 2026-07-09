---
type: how-to
title: "Create an Oracle Database Server Workload"
description: "How to configure an Oracle Database Server Workload in Aembit with username/password credential injection, including optional TLS (TCPS) connections"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/guides/oracle-database/
interface: web-ui
tags: [databases, guide, server-workload, access-policy]
timestamp: 2026-06-30T13:30:29-04:00
---

# Create an Oracle Database Server Workload

This guide walks you through creating a Server Workload**Server Workload**: Server Workloads represent target services, APIs, databases, or applications that receive and respond to access requests from Client Workloads.[Learn more](../../../../get-started/concepts/server-workloads.md) in Aembit to securely connect your applications to Oracle databases without storing static database passwords.

Use this Server Workload to enable your applications to authenticate to Oracle 19c or 21c databases using username/password credentials injected by Aembit’s Agent Proxy.

Aembit intercepts the Oracle Transparent Network Substrate (TNS) wire protocol through transparent steering and injects credentials at connection time. Your client applications connect to Oracle as they normally would, with minor configuration changes described in [Client configuration](#client-configuration).

## Prerequisites

[Section titled “Prerequisites”](#prerequisites)

Before you begin, ensure you have the following:

### Account access

[Section titled “Account access”](#account-access)

* Access to your Aembit Tenant**Aembit Tenant**: Aembit Tenants serve as isolated, dedicated environments within Aembit that provide complete separation of administrative domains and security configurations.[Learn more](../../../../get-started/concepts/administration.md) (role: Workload Administrator or higher)

### Infrastructure

[Section titled “Infrastructure”](#infrastructure)

* Aembit Edge**Aembit Edge**: Aembit Edge represents components deployed within your operational environments that enforce Access Policies by intercepting traffic, verifying identities, and injecting credentials just-in-time.[Learn more](../../../../get-started/concepts/aembit-edge.md) Components deployed on a Linux VM:
* [Agent Proxy installed on Linux](../../../deploy-install/virtual-machine/linux/agent-proxy-install-linux.md)
* [Transparent steering](../../../deploy-install/advanced-options/agent-proxy/selective-transparent-steering.md) (installed by default on Linux). If using selective transparent steering, ensure transparent steering covers the Oracle database host.
* An Oracle 19c or 21c database accessible from the VM (for example, AWS RDS for Oracle or a containerized Oracle instance)
* A database user account with the permissions your application requires
* **(TLS only)** If your Oracle database uses a private or enterprise certificate authority (CA) certificate, add that CA certificate to the Linux VM’s system trust store before enabling TLS. See [Configure TLS connections](#configure-tls-connections).

### Client configuration

[Section titled “Client configuration”](#client-configuration)

Required client settings

Your client application must use `aembit` as the password in its connection configuration. Unlike other protocols Aembit supports, Oracle’s authentication requires the client and Agent Proxy to share an encryption key derived from the password. Using a known password value (`aembit`) allows Agent Proxy to complete this handshake before injecting the real credentials.

## Create the Credential Provider

[Section titled “Create the Credential Provider”](#create-the-credential-provider)

Create a Credential Provider**Credential Provider**: Credential Providers obtain the specific access credentials—such as API keys, OAuth tokens, or temporary cloud credentials—that Client Workloads need to authenticate to Server Workloads.[Learn more](../../../../get-started/concepts/credential-providers.md) that stores the Oracle database credentials Aembit injects at connection time.

1. Log in to your Aembit Tenant.

2. Go to **Credential Providers** and click **+ New**.

3. Configure the following fields:

   | Field               | Value                                                   |
   | ------------------- | ------------------------------------------------------- |
   | **Name**            | Descriptive name (for example, `oracle-db-credentials`) |
   | **Credential Type** | Username & Password                                     |
   | **Username**        | The Oracle database username (for example, `app_user`)  |
   | **Password**        | The Oracle database password                            |

4. Click **Save**.

For detailed configuration options, see [Username & Password Credential Provider](../../credential-providers/username-password.md).

## Create the Server Workload

[Section titled “Create the Server Workload”](#create-the-server-workload)

Create a Server Workload that identifies your Oracle database.

1. Go to **Server Workloads** and click **+ New**.

2. Configure the following fields:

   | Field                        | Value                                                                     |
   | ---------------------------- | ------------------------------------------------------------------------- |
   | **Name**                     | Descriptive name (for example, `oracle-db-production`)                    |
   | **Host**                     | The Oracle database hostname or IP address                                |
   | **Application Protocol**     | Oracle Database                                                           |
   | **Port**                     | `1521` (non-TLS, standard default) or `2484` (TCPS/TLS, standard default) |
   | **TLS** (on Port)            | Check to enable TLS on the client-to-proxy connection (TCPS)              |
   | **Forward to Port**          | `1521` (non-TLS, standard default) or `2484` (TCPS/TLS, standard default) |
   | **TLS** (on Forward to Port) | Check to enable TLS on the proxy-to-database connection                   |
   | **Authentication method**    | Password Authentication                                                   |
   | **Authentication scheme**    | Password                                                                  |

   Note

   You can enable TLS independently on the client-to-proxy side and the proxy-to-database side. Both sides typically use TCPS (port 2484) when you enable TLS end-to-end.

3. Click **Save**.

## Create an Access Policy

[Section titled “Create an Access Policy”](#create-an-access-policy)

Create an Access Policy**Access Policy**: Access Policies define, enforce, and audit access between Client and Server Workloads by cryptographically verifying workload identity and contextual factors rather than relying on static secrets.[Learn more](../../../../get-started/concepts/access-policies.md) that links your Client Workload**Client Workload**: Client Workloads represent software applications, scripts, or automated processes that initiate access requests to Server Workloads, operating autonomously without direct user interaction.[Learn more](../../../../get-started/concepts/client-workloads.md), Credential Provider, and Server Workload.

1. Go to **Access Policies** and click **+ New**.

2. In the Access Policy Builder, configure:

   * **Client Workload**: Select the Client Workload that represents your application connecting to Oracle
   * **Server Workload**: Select the Oracle Database Server Workload you created in the preceding section
   * **Credential Provider**: Select the Username & Password Credential Provider you created in the preceding section

3. (Optional) Add a Trust Provider**Trust Provider**: Trust Providers validate Client Workload identities through workload attestation, verifying identity claims from the workload's runtime environment rather than relying on pre-shared secrets.[Learn more](../../../../get-started/concepts/trust-providers.md) and

   Access Condition**Access Condition**: Access Conditions add dynamic, context-aware constraints to authorization by evaluating circumstances like time, location, or security posture to determine whether to grant access.[Learn more](../../../../get-started/concepts/access-conditions.md) based on your security requirements.

4. Click **Save Policy & Activate**.

For more details on configuring Access Policies, see [Access Policies](../../overview.md).

## Configure TLS connections

[Section titled “Configure TLS connections”](#configure-tls-connections)

Aembit supports TLS for Oracle database connections using Oracle’s TCPS (TCP/IP with TLS) protocol. Enable TLS on the client-to-proxy connection, the proxy-to-database connection, or both.

### Add the Oracle CA certificate to the system trust store

[Section titled “Add the Oracle CA certificate to the system trust store”](#add-the-oracle-ca-certificate-to-the-system-trust-store)

Agent Proxy validates the Oracle database’s TLS certificate using the Linux VM’s system trust store. If your Oracle database uses a certificate from a private or enterprise CA, add that CA certificate to the system trust store before enabling TLS on the Server Workload.

Skip this step if your Oracle database uses a publicly trusted CA certificate or if you’ve already added it.

On Ubuntu and Debian:

```shell
sudo cp your-oracle-ca.crt /usr/local/share/ca-certificates/
sudo update-ca-certificates
```

On RHEL and CentOS:

```shell
sudo cp your-oracle-ca.crt /etc/pki/ca-trust/source/anchors/
sudo update-ca-trust
```

AWS RDS certificates

AWS RDS for Oracle uses certificates from Amazon’s certificate authority. Download and install the RDS CA bundle from the [Amazon RDS SSL/TLS documentation](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/UsingWithRDS.SSL.html) into your system trust store if not already present.

### Enable TLS on the Server Workload

[Section titled “Enable TLS on the Server Workload”](#enable-tls-on-the-server-workload)

1. Go to **Server Workloads** and open your Oracle Database Server Workload.

2. Under **Service Endpoint**, in the **Port** field, check the **TLS** checkbox to enable TCPS on the client-to-proxy connection. Update the port to match the TCPS port your clients connect to (`2484` is standard).

3. In the **Forward to Port** field, check the **TLS** checkbox to enable TCPS on the proxy-to-database connection. Update the forward port to `2484` (or the TCPS port your Oracle database listens on).

4. Click **Save**.

### Update client connection strings for TCPS

[Section titled “Update client connection strings for TCPS”](#update-client-connection-strings-for-tcps)

Update your client applications to connect over TCPS when you enable TLS on the client-to-proxy side.

**Python (oracledb thin):**

```python
import oracledb


# TCPS connection — Agent Proxy listens on port 2484
connection = oracledb.connect(
    user="any_user",
    password="aembit",
    dsn="tcps://your-oracle-host:2484/your_service_name"
)
```

**Java (JDBC thin):**

```java
// TCPS JDBC URL — Agent Proxy listens on port 2484
String url = "jdbc:oracle:thin:@tcps://your-oracle-host:2484/your_service_name";
Connection conn = DriverManager.getConnection(url, "any_user", "aembit");
```

## Test the integration

[Section titled “Test the integration”](#test-the-integration)

After creating the Access Policy, test the connection from your application on the Linux VM where Agent Proxy is running.

### Test with a Python thin client

[Section titled “Test with a Python thin client”](#test-with-a-python-thin-client)

```python
import oracledb


# Connect using thin mode (default), no Oracle Client installation required
# The Agent Proxy intercepts this connection and injects credentials
connection = oracledb.connect(
    user="any_user",
    password="aembit",
    dsn="your-oracle-host:1521/your_service_name"
)


cursor = connection.cursor()
cursor.execute("SELECT 1 FROM DUAL")
print(cursor.fetchone())


cursor.close()
connection.close()
```

Tip

The password **must** be `aembit`. Agent Proxy expects this value and replaces it with the real credentials from the Credential Provider. The username can be any value, because Agent Proxy replaces it too.

The expected result is `(1,)`. Agent Proxy intercepts the Oracle TNS connection, injects the real database credentials from the Credential Provider, and forwards the authenticated connection to the Oracle database.

### Test with a Java thin client

[Section titled “Test with a Java thin client”](#test-with-a-java-thin-client)

```java
// JDBC thin driver, no Oracle Client installation required
// The Agent Proxy intercepts this connection and injects credentials
String url = "jdbc:oracle:thin:@your-oracle-host:1521/your_service_name";
Connection conn = DriverManager.getConnection(url, "any_user", "aembit");


Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery("SELECT 1 FROM DUAL");
rs.next();
System.out.println(rs.getInt(1));


rs.close();
stmt.close();
conn.close();
```

## Troubleshooting

[Section titled “Troubleshooting”](#troubleshooting)

For common issues like Agent Proxy connectivity or network problems, see the [Troubleshooting Guide](../troubleshooting.md).

This section covers Oracle Database-specific issues.

### Connection refused or timeout

[Section titled “Connection refused or timeout”](#connection-refused-or-timeout)

**Symptom:** Your client application receives a connection timeout or “Connection refused” error when connecting to Oracle.

**Cause:** Agent Proxy isn’t running, the Oracle database is unreachable, or the Oracle database host isn’t in the `AEMBIT_STEERING_ALLOWED_HOSTS` list (if you’ve enabled selective transparent steering).

**Solution:**

1. Verify Agent Proxy is running: `sudo systemctl status aembit_agent_proxy`
2. If you’ve enabled [selective transparent steering](../../../deploy-install/advanced-options/agent-proxy/selective-transparent-steering.md), confirm the Oracle database hostname is in the `AEMBIT_STEERING_ALLOWED_HOSTS` list.
3. Test direct connectivity to the Oracle database from the VM: `nc -zv <oracle-host> <port>`

### Authentication fails after credential injection

[Section titled “Authentication fails after credential injection”](#authentication-fails-after-credential-injection)

**Symptom:** The Oracle database returns `ORA-01017: invalid username/password; logon denied` even though the Credential Provider has correct credentials.

**Cause:** The Oracle database user may use a password version that Aembit doesn’t support, or the username/password in the Credential Provider may be incorrect.

**Solution:**

1. Verify your client application uses `aembit` as the password in its connection configuration

2. Verify the credentials in your Credential Provider match the Oracle database user exactly (username is case-sensitive in Oracle)

3. Test the credentials directly against the Oracle database (bypassing Agent Proxy) to confirm they work

4. Check Agent Proxy logs for credential injection errors:

   ```shell
   sudo journalctl --namespace aembit_agent_proxy --since "5 minutes ago"
   ```

### TLS handshake failure

[Section titled “TLS handshake failure”](#tls-handshake-failure)

**Symptom:** The connection from your Oracle client fails with a generic connection error or times out. TLS handshake failures surface in Agent Proxy logs only. Check AP logs for certificate or TLS-related errors.

**Cause:** Agent Proxy can’t validate the Oracle database’s TLS certificate. The CA certificate isn’t in the system trust store, or the Oracle database isn’t configured for TCPS on the expected port.

**Solution:**

1. Verify you’ve installed the Oracle CA certificate in Agent Proxy VM’s system trust store. See [Add the Oracle CA certificate to the system trust store](#add-the-oracle-ca-certificate-to-the-system-trust-store).
2. Confirm the Oracle database is listening on the TCPS port: `nc -zv <oracle-host> 2484`
3. Check that the **TLS** checkbox on **Forward to Port** in the Server Workload matches whether your Oracle database requires TCPS.

### ORA-12170 or ORA-12541 (TNS errors)

[Section titled “ORA-12170 or ORA-12541 (TNS errors)”](#ora-12170-or-ora-12541-tns-errors)

**Symptom:** Oracle client returns `ORA-12170: TNS:Connect timeout` or `ORA-12541: TNS:No listener`.

**Cause:** The client isn’t reaching Agent Proxy, or Agent Proxy isn’t forwarding the Oracle database connection.

**Solution:**

1. Confirm the port in the client connection string matches the **Port** field in the Server Workload.

2. Verify transparent steering is active for the Oracle host and port.

3. Check Agent Proxy logs for any connection errors:

   ```shell
   sudo journalctl --namespace aembit_agent_proxy --since "5 minutes ago"
   ```

### No matching Access Policy

[Section titled “No matching Access Policy”](#no-matching-access-policy)

**Symptom:** The connection succeeds but Agent Proxy doesn’t inject credentials, so the application connects with the placeholder credentials and Oracle rejects the login.

**Cause:** No active Access Policy matches the Client Workload, Server Workload, and Credential Provider combination.

**Solution:**

1. Verify your Access Policy is active (not deactivated) in the Aembit Tenant
2. Confirm the Client Workload identifier matches your application (check process name, path, or other configured identifiers)
3. Check that the Server Workload host and port match the Oracle database your application connects to

## Cleanup

[Section titled “Cleanup”](#cleanup)

If you no longer need this integration, remove components in this order:

Deactivate Access Policies first

You must deactivate any Access Policies that reference the Server Workload or Credential Provider before you can delete those components. Attempting to delete a Server Workload or Credential Provider that an Access Policy uses results in an error.

1. **Deactivate associated Access Policies**

   * Go to **Access Policies**
   * Find policies that use this Server Workload or Credential Provider
   * Deactivate the policy (toggle off)

2. **Delete the Server Workload in Aembit**

   * Go to **Server Workloads**
   * Select your Oracle Database workload and click **Delete**

3. **Delete the Credential Provider in Aembit**

   * Go to **Credential Providers**
   * Select the associated Credential Provider and click **Delete**

Deleting the Server Workload immediately stops credential provisioning. Make sure no applications actively use this workload before deletion.

## Related resources

[Section titled “Related resources”](#related-resources)

* [About Oracle Databases](../../../deploy-install/databases/about-oracle-databases.md): How Aembit’s Oracle protocol support works, supported versions, and thin vs thick clients
* [Username & Password Credential Provider](../../credential-providers/username-password.md): Credential Provider configuration
* [Transparent steering](../../../deploy-install/advanced-options/agent-proxy/selective-transparent-steering.md): Steering mode configuration
* [Access Policies](../../overview.md): How to create and manage Access Policies
* [Support matrix](../../../../reference/support-matrix.md): Supported deployment models for Oracle Database

## Related

**Compatible credential providers**

* [Username & Password](../../credential-providers/username-password.md)
