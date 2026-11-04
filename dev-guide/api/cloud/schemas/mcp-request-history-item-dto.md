---
type: reference
title: "McpRequestHistoryItemDTO"
description: "MCP request transaction history item"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# McpRequestHistoryItemDTO

MCP request transaction history item

**Type:** object

**Properties:**

- **correlationId** *(optional)*: null,string - The transaction correlation ID
- **requestTime** *(optional)*: string (date-time) - Timestamp when the request occurred
- **durationMs** *(optional)*: number (double) - Duration of the request transaction in milliseconds
- **user** *(optional)*: null,string - User identity executing the request
- **mcpClient** *(optional)*: null,string - MCP client workload name
- **mcpMethod** *(optional)*: null,string - MCP method invoked
- **target** *(optional)*: null,string - Target tool, prompt, or resource name
- **result** *(optional)*: null,string - Outcome result (e.g., Success, Error)
- **httpResponseCode** *(optional)*: null,integer (int32) - HTTP response status code
- **serverReached** *(optional)*: boolean - Indicates whether the upstream MCP server was reached
- **exceptionCategory** *(optional)*: null,string - Exception category if the request resulted in an exception
- **exceptionCode** *(optional)*: null,string - Exception code if the request resulted in an exception
- **rawReason** *(optional)*: null,string - Raw failure or error reason if the request resulted in an exception
