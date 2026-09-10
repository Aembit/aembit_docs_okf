---
type: troubleshooting
title: "Troubleshooting Server Workloads"
description: "Diagnose and resolve common Server Workload integration issues"
resource: https://docs.aembit.io/user-guide/access-policies/server-workloads/troubleshooting/
interface: web-ui
tags: ["server-workload", "access-policy"]
timestamp: 2026-09-08T23:32:41-07:00
---

# Troubleshooting Server Workloads

This guide helps you diagnose and resolve common issues when working with Server Workloads.

Each issue follows a Symptom → Diagnosis → Solution → Verification pattern to guide you through systematic troubleshooting.

> **Service-Specific Troubleshooting**
>
> This guide covers universal issues that apply to all Server Workloads. For service-specific issues (like Entra ID permission errors or Salesforce connected app configuration), see the individual [Server Workload guides](guides/overview.md).

## Universal issues

These issues can affect any Server Workload integration, regardless of authentication method.

### Agent Controller not running or disconnected

#### Symptom

* Requests timeout or bypass Aembit entirely
* Application uses placeholder credentials without replacement
* No activity in Aembit logs

#### Diagnosis

Check Agent Controller service status:

**Linux (systemd)**

```shell
systemctl status aembit-agent-controller
# Should show "active (running)"
```

**Windows**

```powershell
Get-Service "Aembit Agent Controller"
# Should show Status: Running
```

**Docker and Kubernetes**

```shell
kubectl get pods -n aembit
# Agent Controller pod should show STATUS: Running
```

Verify Agent Controller status in Aembit console:

1. Go to **Edge Components** > **Agent Controllers**
2. Find your Agent Controller
3. Check **Status**: Should show “Connected” (green indicator)

#### Solution

If Agent Controller has stopped:

```shell
# Linux
sudo systemctl start aembit-agent-controller


# Windows (PowerShell as Administrator)
Start-Service "Aembit Agent Controller"


# Kubernetes
kubectl rollout restart deployment/aembit-agent-controller -n aembit
```

If connection status shows “Disconnected”:

* Check Agent Controller logs for registration or connectivity errors
* Verify the Agent Controller status in the Aembit console
* Check network connectivity to the target service endpoint

#### Verification

Retry your application’s request. It should succeed. Check Agent Proxy logs for credential injection:

```shell
# Linux
sudo journalctl --namespace aembit_agent_proxy -f


# Look for:
# "Request intercepted for server_workload=your-workload-name"
# "Credentials injected successfully"
```

### Network connectivity issues

#### Symptom

* Agent Proxy or application can’t reach target service or Aembit Cloud
* Timeouts when attempting authentication
* DNS resolution failures

#### Diagnosis

Test connectivity to target service (example for Entra ID):

```shell
curl -I "https://login.microsoftonline.com"
# Should return HTTP 200 or 400 (confirms endpoint is reachable)
```

Check DNS resolution for target service:

```shell
nslookup login.microsoftonline.com  # Example for Entra ID
# Should resolve to Microsoft IP addresses
```

Check firewall rules:

* Verify firewall allows outbound HTTPS (port 443) to target service domain
* Check network security groups (cloud environments)
* Check corporate firewall rules (on-premises)

#### Solution

Configure firewall to allow outbound HTTPS traffic:

* Add target service domains (for example, `*.microsoftonline.com` for Entra ID, `api.github.com` for GitHub)

If using a corporate HTTP proxy:

```shell
# Set proxy environment variables for Agent Controller
export HTTP_PROXY=http://proxy.company.com:8080
export HTTPS_PROXY=http://proxy.company.com:8080


# Restart Agent Controller to apply
sudo systemctl restart aembit-agent-controller
```

If DNS resolution fails:

* Verify DNS server configuration in `/etc/resolv.conf` (Linux)
* Add custom DNS servers if needed
* Check that corporate DNS can resolve public domains

#### Verification

Retry the curl command to the target service. It should succeed:

```shell
curl -I "https://target-service.com"  # HTTP 200 or 400 (reachable)
```

Then retry the authentication request from your application.

### Agent Proxy not intercepting traffic

#### Symptom

* Application makes requests but continues using placeholder credentials
* Aembit logs show no activity
* Requests reach target service with placeholder values (visible in service logs)

#### Diagnosis

Verify Agent Controller configuration for traffic interception:

```shell
# View Agent Controller configuration
cat /etc/aembit/agent-controller/config.yaml  # Linux
# Or: C:\Program Files\Aembit\Agent Controller\config.yaml  # Windows


# Verify Server Workload is listed in configuration
```

Check Agent Proxy logs for interception activity:

```shell
# Linux - view real-time logs
sudo journalctl --namespace aembit_agent_proxy -f


# Linux - search for credential-related entries
sudo journalctl --namespace aembit_agent_proxy | grep -i "intercept\|credential"


# Look for log entries like:
# "Request intercepted for server_workload=your-workload-name"
# "Credentials injected successfully"
```

#### Solution

Three causes account for most interception failures.

If the Agent Controller is using an outdated configuration, restart it to reload the configuration:

```shell
sudo systemctl restart aembit-agent-controller  # Linux
# Or restart service in Windows Services
```

If the application isn’t routing traffic through Agent Proxy, check the application’s HTTP proxy environment variables:

```shell
echo $HTTP_PROXY
echo $HTTPS_PROXY
# Should point to Agent Proxy (typically http://localhost:8080)
```

Set the proxy environment variables before starting the application:

```shell
export HTTP_PROXY=http://localhost:8080
export HTTPS_PROXY=http://localhost:8080
your-application-start-command
```

If the application uses the system trust store but the Aembit CA certificate isn’t installed, the application logs show SSL certificate verification errors. Check whether the Aembit CA certificate is in the system trust store:

```shell
# Linux
ls /etc/pki/ca-trust/source/anchors/ | grep -i aembit


# macOS
security find-certificate -c "Aembit" /Library/Keychains/System.keychain
```

If the certificate is missing, install it. See [TLS Decrypt configuration](../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md) for detailed instructions.

#### Verification

After applying fixes, verify Agent Proxy intercepts requests:

```shell
# Monitor Agent Proxy logs while running your application
sudo journalctl --namespace aembit_agent_proxy -f  # Linux


# Trigger authentication request from your application
# (Run your app or call token acquisition method)


# Expected log output:
# "Request intercepted for server_workload=your-workload-name"
# "Credentials injected successfully"
# "Response returned to application"
```

If you see these log entries, Agent Proxy is correctly intercepting requests.

### TLS Decrypt configuration issues

#### Symptom

* SSL certificate verification errors in application logs
* `SSLError: certificate verify failed`
* `CERT_UNTRUSTED` errors

#### Diagnosis

Determine if your Server Workload requires TLS Decrypt:

* Most Server Workloads require TLS Decrypt for intercepting HTTPS traffic
* Not required for plain HTTP traffic

Verify TLS Decrypt configuration in Aembit console:

1. Go to **Edge Components** > **Agent Controllers** and check the **TLS** column for your Agent Controller. This column reports the expiry of the Agent Controller’s own TLS certificate rather than whether TLS Decrypt is configured. A green check mark or a blue information icon means the certificate is healthy, an amber minus sign means it expires within 30 days, and a red exclamation mark means it expired or expires within a week. Hover the icon to read the certificate’s serial number and expiry date. An empty **TLS** cell means the Agent Controller is inactive.
2. Go to **Edge Components** > **Certificates** and click **Download Tenant Root CA**. The Tenant Root CA card shows no validity information and the button stays enabled in every state, so a successful download is what confirms the certificate. The **Not Before - Not After** dates on that page belong to the Standalone Certificate Authorities table.

Verify you installed the CA certificate on your system:

```shell
# Linux - check system trust store
ls /etc/pki/ca-trust/source/anchors/ | grep -i aembit
# Or: ls /usr/local/share/ca-certificates/ | grep -i aembit


# macOS - check keychain
security find-certificate -c "Aembit" /Library/Keychains/System.keychain


# Windows - check certificate store
certutil -store Root | findstr Aembit
```

#### Solution

First, download your Aembit Tenant Root CA from the Aembit console:

1. Go to **Edge Components** > **Certificates**
2. Click **Download Tenant Root CA**

Next, install the CA certificate on your system:

**Linux (CentOS/Red Hat Enterprise Linux)**

```shell
# Copy CA certificate to trust store
sudo cp aembit-ca.crt /etc/pki/ca-trust/source/anchors/


# Update trust store
sudo update-ca-trust
```

**Linux (Ubuntu/Debian)**

```shell
# Copy CA certificate to trust store
sudo cp aembit-ca.crt /usr/local/share/ca-certificates/


# Update trust store
sudo update-ca-certificates
```

**macOS**

```shell
# Add to system keychain
sudo security add-trusted-cert -d -r trustRoot \
  -k /Library/Keychains/System.keychain aembit-ca.crt
```

**Windows (PowerShell as Administrator)**

```powershell
# Import to Trusted Root Certification Authorities
Import-Certificate -FilePath "aembit-ca.crt" -CertStoreLocation Cert:\LocalMachine\Root
```

Finally, restart the application to use the updated trust store.

#### Verification

Retry the request that was failing with SSL errors. It should now succeed without certificate verification errors.

Check application logs - no more `SSLError` or `CERT_UNTRUSTED` messages.

## OAuth-specific issues

These issues apply to Server Workloads using OAuth authentication (Entra ID, Salesforce, GitHub OAuth, etc.).

### OAuth token request fails

This issue applies to Entra ID, Salesforce, GitHub (OAuth mode), and Okta (OAuth mode).

#### Symptom

* Token endpoint returns HTTP 400 Bad Request
* Token endpoint returns HTTP 401 Unauthorized
* Application logs show “invalid\_client” or “unauthorized\_client” errors

#### Diagnosis

Check token endpoint configuration in Server Workload:

1. Go to **Workloads** > **Server Workloads** in Aembit console

2. Select your Server Workload

3. Verify **Token Endpoint** URL is correct:

   * Entra ID: `https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token`
   * Salesforce: `https://{instance}.my.salesforce.com/services/oauth2/token`
   * GitHub: `https://github.com/login/oauth/access_token`

Check Credential Provider configuration:

1. Go to **Access Policies** > **Credential Providers**
2. Select the Credential Provider used by your Access Policy
3. Verify **Client ID** matches the application registration in the OAuth provider
4. For Authorization Code flow, verify **Client Secret** is current

Check Agent Proxy logs for specific error:

```shell
sudo journalctl --namespace aembit_agent_proxy | grep -i "token\|oauth\|error"


# Look for errors like:
# "Token request failed: invalid_client"
# "OAuth provider returned 401"
```

#### Solution

If token endpoint URL is incorrect:

1. Update Server Workload configuration with correct URL
2. Save changes
3. Restart Agent Controller: `sudo systemctl restart aembit-agent-controller`

If Client ID or Secret is incorrect:

1. Verify credentials in OAuth provider (for example, Entra ID App Registration)
2. Update Credential Provider with correct values
3. Save changes
4. Test integration

If using Entra ID and getting “invalid\_client”:

* Verify the application registration exists and isn’t deleted
* Check that the Directory (tenant) ID in token endpoint matches your Entra ID tenant
* Verify you granted API permissions (see [Permission or Scope Errors](#permission-or-scope-errors))

#### Verification

Retry the token request. It should return HTTP 200 with an `access_token` in the response:

```shell
# Check application logs for successful token acquisition
# Expected: "Successfully received access token"
# Response should contain: access_token, expires_in, token_type
```

### Permission or scope errors

This issue applies to Entra ID, Salesforce, and GitHub (OAuth mode).

#### Symptom

* Token request succeeds (HTTP 200)
* But API calls return HTTP 403 Forbidden
* Error messages like “insufficient\_permissions” or “access\_denied”

#### Diagnosis

Check granted scopes vs. required scopes:

For Entra ID:

1. Log in to Azure Portal
2. Go to **Azure Active Directory** > **App registrations**
3. Select your application
4. Click **API permissions**
5. Review granted permissions - verify the list includes all required permissions
6. Check **Status** column - should show green checkmark (administrator consent granted)

For Salesforce:

1. Log in to Salesforce
2. Go to **Setup** > **Apps** > **App Manager**
3. Find your connected app
4. Click **View** → **Manage Consumer Details**
5. Review **Selected OAuth Scopes**

For GitHub:

1. Log in to GitHub
2. Go to **Settings** > **Developer settings** > **GitHub Apps**
3. Select your app
4. Review **Permissions** section
5. Verify you selected the required permissions

Check scope configuration in Aembit Server Workload:

1. Go to **Workloads** > **Server Workloads**
2. Select your Server Workload
3. Verify **Scopes** field contains the required scopes
4. Compare with API documentation for required scopes

#### Solution

If permissions are missing in OAuth provider:

1. Add required permissions in the OAuth provider (Azure Portal, Salesforce, GitHub)
2. For Entra ID: Click **Grant administrator consent** after adding permissions
3. Test the integration again

If scopes are incorrect in Server Workload:

1. Update **Scopes** field in Server Workload configuration
2. Save changes
3. Restart Agent Controller to reload configuration
4. Retry the request

If using Entra ID `.default` scope:

* Verify the target API application defines the permissions your app needs
* If permissions are recently added, wait 5-10 minutes for Azure AD to propagate changes
* Consider using specific scopes instead of `.default` for better visibility

#### Verification

Retry authentication to the protected resource:

```shell
# API call should now return HTTP 200-299 (success)
# No more 403 Forbidden errors
```

Check OAuth provider logs (if available):

* **Entra ID**: **Azure Active Directory** > **Sign-in logs** → Filter by Application ID → Verify successful sign-ins (Status: Success)
* **GitHub**: Check app installation logs
* **Salesforce**: **Setup** > **Event Monitoring** → Check API events

## API key issues

These issues apply to Server Workloads using API Key authentication (Okta, Claude, OpenAI, etc.).

### Invalid API key errors

This issue applies to Okta, Claude, OpenAI, GitHub (API Key mode), Stripe, and Box.

#### Symptom

* API returns HTTP 401 Unauthorized
* Error messages like “Invalid API key” or “Authentication failed”
* Application logs show authentication errors

#### Diagnosis

Verify API key in Credential Provider is current and valid:

1. Go to **Access Policies** > **Credential Providers** in Aembit console
2. Select the Credential Provider used by your Access Policy
3. Review the API key value (Aembit may mask this value)

Check if the target service expired or revoked the API key:

For Okta:

1. Log in to Okta Admin Console
2. Go to **Security** > **API** > **Tokens**
3. Verify your token appears in the list with Status “Active”
4. Check expiration date

For OpenAI and Claude:

1. Log in to provider dashboard
2. Go to API keys section
3. Verify key is active (not revoked)

For GitHub:

1. Go to **Settings** > **Developer settings** > **Personal access tokens**
2. Verify token is active and has required scopes

Check Agent Proxy logs for specific error:

```shell
sudo journalctl --namespace aembit_agent_proxy | grep -i "api.key\|401\|unauthorized"


# Look for errors like:
# "API request returned 401 Unauthorized"
# "Invalid API key format"
```

#### Solution

If the API key expired or the service revoked it:

1. Generate a new API key in the target service (Okta, OpenAI, GitHub, etc.)
2. Copy the new API key
3. Update Credential Provider in Aembit console with new key
4. Save changes
5. Test the integration

If API key format is incorrect:

* **Okta**: Ensure format uses Single Sign-On Web Services (SSWS) like `SSWS {token}` (note the space after SSWS)
* **OpenAI**: Ensure format is `sk-...` (starts with `sk-`)
* **Claude**: Ensure format is `sk-ant-...` (starts with `sk-ant-`)
* **GitHub**: Ensure format is `ghp_...` (classic) or `github_pat_...` (fine-grained)

If header injection isn’t working:

1. Verify **Authentication Scheme** in Server Workload configuration:

   * Bearer: `Authorization: Bearer {api_key}`
   * Header: Custom header name like `X-API-Key: {api_key}`

2. Check **Header** field matches what the service expects

3. Verify you set **Authentication Method** to “API Key” or “HTTP Authentication”

#### Verification

Retry the API request. It should return HTTP 200-299 (success):

```shell
# Check application logs for successful API call
# Expected: HTTP 200 response with valid data
# No more 401 Unauthorized errors
```

Test with curl (for debugging):

```shell
# This won't go through Aembit, but verifies the API key itself works
curl -H "Authorization: Bearer YOUR_API_KEY" https://api.service.com/endpoint


# Should return valid response
```

## Database connection issues

These issues apply to Server Workloads using database authentication (MySQL, Postgres, Redis, etc.).

### Connection refused or timeout

This issue applies to MySQL, PostgreSQL, Redis, and Snowflake.

#### Symptom

* Database connection fails with timeout
* `Connection refused` errors
* Can’t establish connection to database server

#### Diagnosis

Check database server is running and accessible:

**Cloud databases (AWS RDS, GCP Cloud SQL)**

```shell
# Test network connectivity
nc -zv database.example.com 3306  # MySQL
nc -zv database.example.com 5432  # PostgreSQL
nc -zv database.example.com 6379  # Redis


# Should show: Connection to database.example.com port XXXX succeeded
```

**Local databases**

```shell
# Check if database service is running
systemctl status mysql      # MySQL
systemctl status postgresql # PostgreSQL
systemctl status redis      # Redis
```

Check firewall and security group rules:

For AWS RDS:

1. Go to RDS console
2. Select your database instance
3. Click **Connectivity & security** tab
4. Review **Security groups** - verify the rules allow your application’s IP or security group
5. Verify **Publicly accessible** setting matches your network topology

For GCP Cloud SQL:

1. Go to Cloud SQL console
2. Select your instance
3. Click **Connections** tab
4. Verify **Authorized networks** includes your application’s IP range

**On-premises (Linux)**

```shell
# Check firewall rules (Linux)
sudo iptables -L | grep 3306  # MySQL
sudo iptables -L | grep 5432  # PostgreSQL
```

Check Server Workload configuration:

1. Go to **Workloads** > **Server Workloads** in Aembit console
2. Verify **Host** matches database server hostname or IP
3. Verify **Port** is correct (3306 for MySQL, 5432 for Postgres, 6379 for Redis)

#### Solution

If database service isn’t running:

```shell
# Start database service
sudo systemctl start mysql      # MySQL
sudo systemctl start postgresql # PostgreSQL
sudo systemctl start redis      # Redis
```

If security group blocks connection:

1. Add inbound rule allowing traffic from application’s IP or security group
2. For AWS RDS: Add rule for TCP port 3306 (MySQL) or 5432 (Postgres) or 6379 (Redis)
3. For on-premises: Update firewall rules to allow traffic

If using private network:

* Verify you configured Virtual Private Network (VPN) or Virtual Private Cloud (VPC) peering
* Check route tables allow traffic between application and database subnets
* Test connectivity from application server: `telnet database.example.com 3306`

#### Verification

Retry the database connection from your application. It should succeed:

```shell
# Test with database client
mysql -h database.example.com -u username -p  # MySQL (will prompt for password)
psql -h database.example.com -U username      # PostgreSQL


# Connection should establish without timeout
```

Then verify application can connect through Aembit.

### Authentication failed

This issue applies to MySQL, PostgreSQL, and Snowflake.

#### Symptom

* Connection reaches database but login fails
* `Access denied for user` errors (MySQL)
* `password authentication failed` errors (PostgreSQL)
* Database connection timeout after authentication attempt

#### Diagnosis

Check Credential Provider configuration in Aembit:

1. Go to **Access Policies** > **Credential Providers**
2. Select the Credential Provider for your database
3. Verify **Username** matches database user
4. Verify **Password** is correct
5. For AWS RDS with IAM authentication, verify IAM role and token generation

Check database user permissions:

**MySQL**

```sql
-- Connect as database admin
mysql -u root -p


-- Check if user exists
SELECT User, Host FROM mysql.user WHERE User='your_username';


-- Check user permissions
SHOW GRANTS FOR 'your_username'@'%';
```

**PostgreSQL**

```sql
-- Connect as database admin
psql -U postgres


-- Check if user exists
\du your_username


-- Check database access
\l


-- Verify user has CONNECT privilege
SELECT datname, datacl FROM pg_database WHERE datname='your_database';
```

Check authentication method in database configuration:

**MySQL: /etc/mysql/mysql.conf.d/mysqld.cnf**

```ini
# Verify authentication plugin
default_authentication_plugin=mysql_native_password  # or caching_sha2_password
```

**PostgreSQL: /var/lib/pgsql/data/pg\_hba.conf**

```plaintext
# Verify connection allowed for your user
# Example:
host    all    your_username    0.0.0.0/0    md5
```

#### Solution

If username or password is incorrect in Credential Provider:

1. Verify credentials by testing direct connection to database
2. Update Credential Provider with correct credentials
3. Save changes
4. Retry connection through Aembit

If database user doesn’t exist:

**MySQL**

```sql
-- Create user
CREATE USER 'your_username'@'%' IDENTIFIED BY 'your_password';


-- Grant permissions
GRANT ALL PRIVILEGES ON your_database.* TO 'your_username'@'%';
FLUSH PRIVILEGES;
```

**PostgreSQL**

```sql
-- Create user
CREATE USER your_username WITH PASSWORD 'your_password';


-- Grant permissions
GRANT ALL PRIVILEGES ON DATABASE your_database TO your_username;
```

If using AWS RDS IAM authentication:

1. Verify IAM policy allows `rds-db:connect` action

2. Verify you created the database user with IAM authentication:

   ```sql
   CREATE USER your_username IDENTIFIED WITH AWSAuthenticationPlugin AS 'RDS';
   ```

3. Verify you configured the Credential Provider for IAM authentication

#### Verification

Retry database connection. It should succeed:

```shell
# Application logs should show successful connection
# Expected: "Database connection established"
# No more "Access denied" or "authentication failed" errors
```

Test query execution:

```python
cursor.execute("SELECT 1")
result = cursor.fetchone()
print(result)  # Should print: (1,)
```

### Unsupported MySQL authentication plugin

This issue applies to MySQL.

#### Symptom

* MySQL connection fails during authentication even though the username and password are correct
* The client receives an error similar to: `Aembit: An error occurred during authentication: The server specified use of authentication plugin "<plugin>", which is not supported.`
* Aembit logs an Error-severity Workload Event

#### Diagnosis

Aembit injects MySQL credentials by participating in the MySQL authentication handshake, so the database account must use an authentication plugin that Aembit supports. The supported plugins are:

* `mysql_native_password`
* `caching_sha2_password` (the default since MySQL 8.0)
* `mysql_old_password` (legacy)

Any other plugin (for example, `sha256_password`, `auth_socket`, `authentication_ldap_*`, `authentication_kerberos`, or MariaDB’s `ed25519` and PAM plugins) isn’t supported. When the server selects an unsupported plugin, Aembit returns the preceding error instead of injecting credentials.

Check which plugin the database account uses:

```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'your_username';
```

#### Solution

Configure the account to use a supported plugin. `caching_sha2_password` is the modern MySQL default:

```sql
ALTER USER 'your_username'@'%' IDENTIFIED WITH caching_sha2_password BY 'your_password';
FLUSH PRIVILEGES;
```

To use native password authentication instead:

```sql
ALTER USER 'your_username'@'%' IDENTIFIED WITH mysql_native_password BY 'your_password';
FLUSH PRIVILEGES;
```

If you can’t change the account’s plugin (for example, the database enforces an external plugin such as Lightweight Directory Access Protocol (LDAP) or Kerberos), Aembit credential injection isn’t available for that account.

#### Verification

Confirm the account now reports a supported plugin:

```sql
SELECT user, host, plugin FROM mysql.user WHERE user = 'your_username';
-- plugin should be caching_sha2_password or mysql_native_password
```

Then retry the connection through Aembit. It should authenticate successfully, and the corresponding [Workload Event](../../audit-report/workload-events/overview.md) should show an outcome of `Modified` rather than `Error`.

## Next steps

If you’re still experiencing issues after following these troubleshooting steps:

1. **Check service-specific guides**: See [Server Workload Guides](guides/overview.md) for service-specific troubleshooting
2. **Review architecture**: See [Architecture Patterns](architecture-patterns.md) to understand expected data flow
3. **Contact support**: Provide Agent Controller logs and specific error messages for faster resolution

## Related resources

* **[Architecture Patterns](architecture-patterns.md)** - Understanding data flow for each authentication method
* **[Client library patterns for Agent Proxy](../../../dev-guide/integration/client-library-patterns.md)** - Placeholder credentials and integration patterns
* **[Test and debug your integration](../../../dev-guide/integration/testing.md)** - Verify credential delivery end to end
* **[Server Workload Guides](guides/overview.md)** - Service-specific configuration
* **[TLS Decrypt Configuration](../../deploy-install/advanced-options/tls-decrypt/configure-tls-decrypt.md)** - Detailed TLS setup
* **[Agent Controller](../../deploy-install/about-agent-controller.md)** - Understanding the Agent Controller
