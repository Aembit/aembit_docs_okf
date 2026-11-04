---
type: reference
title: "McpRequestDetailDTO"
description: "Detailed information for an MCP request transaction"
resource: https://docs.aembit.io/dev-guide/api/cloud/api-reference-cloud/
interface: api
timestamp: 2026-11-03T18:06:18-08:00
---

# McpRequestDetailDTO

Detailed information for an MCP request transaction

**Type:** object

**Properties:**

- **correlationId** *(optional)*: null,string - The transaction correlation ID
- **requestTime** *(optional)*: string (date-time) - Timestamp when the request occurred
- **durationMs** *(optional)*: number (double) - Duration of the request transaction in milliseconds
- **user** *(optional)*: null,string - User identity executing the request
- **mcpClient** *(optional)*: null,string - MCP client workload name
- **mcpServer** *(optional)*: null,string - MCP server workload name
- **mcpMethod** *(optional)*: null,string - MCP method invoked
- **target** *(optional)*: null,string - Target tool, prompt, or resource name
- **result** *(optional)*: null,string - Outcome result
- **httpResponseCode** *(optional)*: null,integer (int32) - HTTP response status code
- **serverReached** *(optional)*: boolean - Indicates whether the upstream MCP server was reached
- **exceptionCategory** *(optional)*: null,string - Exception category if request resulted in an exception
- **exceptionCode** *(optional)*: null,string - Exception code if request resulted in an exception
- **rawReason** *(optional)*: null,string - Raw reason or error message
- **clientAccessDecision** *(optional)*: null,string - Client access policy decision
- **clientAccessPolicy** *(optional)*: null,string - Client access policy name
- **serverAccessDecision** *(optional)*: null,string - Server access policy decision
- **serverAccessPolicy** *(optional)*: null,string - Server access policy name
- **accessDecisions** *(optional)*: null,array - Bilateral access policy decisions
- **serverOutcomes** *(optional)*: null,array - Per-server outcomes
- **relevantEvents** *(optional)*: null,array - Chronological relevant events for this request
