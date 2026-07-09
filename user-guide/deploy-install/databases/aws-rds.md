---
type: how-to
title: "AWS Relational Database Service (RDS) Certificates"
description: "How to install AWS RDS Certificate to Agent Proxy to make it trust the AWS RDS Certificate"
resource: https://docs.aembit.io/user-guide/deploy-install/databases/aws-rds/
interface: web-ui
tags: [database, deploy-install]
timestamp: 2026-02-26T15:41:06-08:00
type_inferred: true
---

# AWS Relational Database Service (RDS) Certificates

Note

MySQL, PostgreSQL, and Redshift in AWS uses a TLS certificate issued from an AWS root certificate authority that’s not publicly trusted.

You must follow the steps on this page when attempting to connect to MySQL, PostgreSQL, and Redshift in AWS.

To install all the possible CA Certificates for AWS RDS databases, follow the instructions and use the following commands:

1. Transition to a root session so you have root access.

```shell
sudo su
```

2. Run the following commands to download the CA certificate bundle from AWS, split it into a set of `.crt` files, and then update the local trust store with all these files.

```shell
apt update ; apt install -y ca-certificates curl
rm -f /tmp/global-bundle.pem
curl "https://truststore.pki.rds.amazonaws.com/global/global-bundle.pem" -o /tmp/global-bundle.pem
csplit -s -z -f /usr/local/share/ca-certificates/aws-rds /tmp/global-bundle.pem '/-----BEGIN CERTIFICATE-----/' '{*}'
for file in /usr/local/share/ca-certificates/aws-rds*; do mv -- "$file" "${file%}.crt"; done
update-ca-certificates
```

3. After running this command, you should see the following output:

```shell
Updating certificates in /etc/ssl/certs...
118 added, 0 removed; done.
```

4. Ensure you exit your root session.

```shell
exit
```

Note

Make sure to follow the preceding instructions for each virtual machine running Client Workloads that needs access to AWS MySQL, PostgreSQL, or Redshift.
