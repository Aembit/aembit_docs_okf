---
type: reference
title: "McpServerOutcomeDTO"
description: "Outcome for a specific MCP server within a request"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# McpServerOutcomeDTO

Outcome for a specific MCP server within a request

**Type:** object

**Properties:**

- **serverId** *(optional)*: null,string (uuid) - MCP server workload ID if known
- **server** *(optional)*: null,string - MCP server workload name
- **status** *(optional)*: null,string - Server outcome status
- **httpResponseCode** *(optional)*: null,integer (int32) - HTTP response status code
- **durationMs** *(optional)*: null,number (double) - Duration in milliseconds
- **exceptionCategory** *(optional)*: null,string - Exception category if the server outcome was an error
- **exceptionCode** *(optional)*: null,string - Exception code if the server outcome was an error
- **rawReason** *(optional)*: null,string - Raw reason or error message
