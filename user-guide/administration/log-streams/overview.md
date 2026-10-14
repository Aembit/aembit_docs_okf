---
type: reference
title: "Log Stream overview"
description: "Description of what Log Streams are and how to capture and archive log information"
resource: https://docs.aembit.io/user-guide/administration/log-streams/
interface: web-ui
tags: ["log-stream", "administration"]
timestamp: 2026-10-08T16:14:07-07:00
---

# Log Stream overview

The Log Streams feature enables you to forward audit logs, workload events, and access authorization events from your Aembit Tenant to an AWS S3 or GCP Cloud Storage Bucket. This also enables you to perform more detailed data analysis and processing outside of your Aembit Tenant.

This is a paid feature.

> **Not enabled by default**
>
> Contact [Aembit support](https://aembit.io/contact) to enable this feature.

The following pages provide information about configuring Log Streams for different cloud storage services:

* [AWS S3](aws-s3.md) - Configure Log Streams to send logs to AWS S3 buckets
* [CrowdStrike Next-Gen SIEM](crowdstrike-siem.md) - Configure Log Streams to send logs to CrowdStrike Next-Gen SIEM.
* [Google Cloud Storage (GCS) Bucket](gcs-bucket.md) - Configure Log Streams to send logs to Google Cloud Storage buckets
* [Splunk SIEM](splunk-siem.md) - Configure Log Streams to send logs to Splunk SIEM
