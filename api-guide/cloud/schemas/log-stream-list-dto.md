---
type: reference
title: "LogStreamListDTO"
description: "Page of Log Streams"
resource: https://docs.aembit.io/api-guide/cloud/api-reference-cloud/
interface: api
timestamp: 2025-08-28T08:38:37-04:00
---

# LogStreamListDTO

Page of Log Streams

**Type:** object

**Properties:**

- **page** *(optional)*: integer (int32) - Page of entities
- **perPage** *(optional)*: integer (int32) - Number of entities requested for the current page
- **order** *(optional)*: string | null - Ordering criteria used for the current page
- **statusCode** *(optional)*: integer (int32) - HTTP Status Code of the response
- **recordsTotal** *(optional)*: integer (int32) - Total number of Log Streams
- **logStreams** *(optional)*: Array of [LogStreamDTO](log-stream-dto.md) - Page of Log Streams
