---
type: reference
title: "MCP exception codes"
description: "The exception categories and codes that the MCP Access Exceptions report shows, with their messages"
resource: https://docs.aembit.io/user-guide/audit-report/mcp/exception-codes/
interface: web-ui
tags: ["mcp", "audit-report"]
timestamp: 2026-11-03T15:14:28-05:00
---

# MCP exception codes

[MCP Access Exceptions](access-exceptions.md) shows an exception category and an exception code for each row. This page lists every category and code that its **Category** and **Exception Code** filters offer, with the message that appears when you hover over a code.

The report shows each category’s name and each code’s label. Each Workload Event records the same exception in `application.mcp.exceptionCategory` and `application.mcp.exceptionCode`, as the values in the following tables. For those fields, see [Supported protocols and application fields](../workload-events/supported-protocols.md).

## Categories

| Category             | `exceptionCategory` value |
| -------------------- | ------------------------- |
| Access Policy        | `ACCESS_POLICY`           |
| AIDR                 | `AIDR`                    |
| Authorization Server | `AUTHORIZATION_SERVER`    |
| Bad Request          | `BAD_REQUEST`             |
| Edge API             | `EDGE_API`                |
| Elicitation          | `ELICITATION`             |
| MCP Client           | `MCP_CLIENT`              |
| MCP Gateway          | `MCP_GATEWAY`             |
| MCP Server           | `MCP_SERVER`              |
| Output Schema        | `OUTPUT_SCHEMA`           |
| Tasks                | `TASKS`                   |
| Tool Filter          | `TOOL_FILTER`             |

## Exception codes

Three codes appear in two categories, with a different message in each: `INTERNAL_ERROR`, `LISTEN_FAILED`, and `UNSUPPORTED_PROTOCOL_VERSION`. Read the category to tell them apart.

Three codes have `Warning` severity: `CONFLICTING_RESOURCE_URI`, `AIDR_UNAVAILABLE_FAIL_OPEN_BYPASS`, and `UNSUPPORTED_METHOD`. Every other code has `Error` severity.

| Category             | Exception Code                    | `exceptionCode` value               | Message                                                                              |
| -------------------- | --------------------------------- | ----------------------------------- | ------------------------------------------------------------------------------------ |
| Access Policy        | mTLS Over HTTP                    | `MTLS_OVER_HTTP`                    | `mTLS is configured, but the server workload accepts only HTTP.`                     |
| Access Policy        | Invalid Server Workload URL       | `INVALID_SERVER_WORKLOAD_URL`       | `Server workload service endpoint is not a valid URL.`                               |
| Access Policy        | Missing Service Endpoint          | `MISSING_SERVICE_ENDPOINT`          | `Server workload has no service endpoint configured.`                                |
| Access Policy        | Conflicting Workload Prefix       | `CONFLICTING_WORKLOAD_PREFIX`       | `Several assigned MCP servers share the same sanitized name prefix.`                 |
| Access Policy        | Conflicting Resource URI          | `CONFLICTING_RESOURCE_URI`          | `Several assigned MCP servers expose the same resource URI; the first one keeps it.` |
| Access Policy        | Credential Not Applicable         | `CREDENTIAL_NOT_APPLICABLE`         | `Credential provider configuration cannot be applied to the MCP server request.`     |
| AIDR                 | Tool Input Blocked                | `TOOL_INPUT_BLOCKED`                | `Tool call input was blocked by the AIDR policy.`                                    |
| AIDR                 | Tool Output Blocked               | `TOOL_OUTPUT_BLOCKED`               | `Tool call output was blocked by the AIDR policy.`                                   |
| AIDR                 | Tool Listing Blocked              | `TOOL_LISTING_BLOCKED`              | `Tool listing was blocked by the AIDR policy.`                                       |
| AIDR                 | AIDR Unavailable                  | `AIDR_UNAVAILABLE`                  | `AIDR service is unavailable: content was blocked (fail-closed).`                    |
| AIDR                 | AIDR Unavailable Fail-Open Bypass | `AIDR_UNAVAILABLE_FAIL_OPEN_BYPASS` | `AIDR service is unavailable: content was passed through uninspected (fail-open).`   |
| Authorization Server | Missing Claims                    | `MISSING_CLAIMS`                    | `Access token is missing required claims.`                                           |
| Authorization Server | Invalid Claims                    | `INVALID_CLAIMS`                    | `Access token claims are not accepted.`                                              |
| Bad Request          | Identifier Not Found              | `IDENTIFIER_NOT_FOUND`              | `Requested tool, prompt, resource, or task is not on any assigned MCP server.`       |
| Edge API             | No MCP Servers Assigned           | `NO_ASSIGNED_WORKLOADS`             | `No MCP servers are assigned to this client.`                                        |
| Edge API             | Missing Assessment Claims         | `MISSING_ASSESSMENT_CLAIMS`         | `Workload assessment is missing required claims.`                                    |
| Edge API             | Internal Gateway Error            | `INTERNAL_ERROR`                    | `Aembit Cloud failed to process the request.`                                        |
| Edge API             | Subscription Listen Failed        | `LISTEN_FAILED`                     | `Notification stream from Aembit Cloud failed.`                                      |
| Edge API             | Credential Unavailable            | `CREDENTIAL_UNAVAILABLE`            | `Aembit Cloud could not provide credentials for the MCP server.`                     |
| Elicitation          | Elicitation Failed                | `ELICITATION_FAILED`                | `Elicitation request to the MCP client failed.`                                      |
| Elicitation          | Input Rounds Exceeded             | `INPUT_ROUNDS_EXCEEDED`             | `MCP server requested input for more rounds than the gateway allows.`                |
| MCP Client           | Invalid Audience                  | `INVALID_AUDIENCE`                  | `Access token audience does not match the gateway.`                                  |
| MCP Client           | Malformed JSON-RPC Request        | `MALFORMED_REQUEST`                 | `MCP client request is not a valid JSON-RPC message.`                                |
| MCP Client           | Invalid HTTP Request              | `INVALID_HTTP_REQUEST`              | `MCP client request violates the Streamable HTTP transport specification.`           |
| MCP Client           | Host Not Allowed                  | `HOST_NOT_ALLOWED`                  | `Request Host or Origin header value is not allowed by the gateway.`                 |
| MCP Client           | Unsupported Protocol Version      | `UNSUPPORTED_PROTOCOL_VERSION`      | `MCP client requested an unsupported protocol version.`                              |
| MCP Client           | Missing Session ID                | `MISSING_SESSION_ID`                | `MCP session ID is missing.`                                                         |
| MCP Client           | Session Unavailable               | `SESSION_UNAVAILABLE`               | `MCP session was not found or has expired.`                                          |
| MCP Client           | Client Terminated                 | `CLIENT_TERMINATED`                 | `MCP client closed the connection unexpectedly.`                                     |
| MCP Gateway          | Internal Gateway Error            | `INTERNAL_ERROR`                    | `MCP gateway failed to process the request.`                                         |
| MCP Gateway          | Unsupported MCP Method            | `UNSUPPORTED_METHOD`                | `MCP method is not supported by the gateway.`                                        |
| MCP Server           | Server Unreachable                | `SERVER_UNREACHABLE`                | `MCP server is unreachable.`                                                         |
| MCP Server           | Unsupported Protocol Version      | `UNSUPPORTED_PROTOCOL_VERSION`      | `MCP server supports no protocol version compatible with the gateway.`               |
| MCP Server           | HTTP Error                        | `HTTP_ERROR`                        | `MCP server returned an HTTP error.`                                                 |
| MCP Server           | Redirect Refused                  | `REDIRECT_REFUSED`                  | `MCP server returned a redirect, which the gateway forbids.`                         |
| MCP Server           | Internal Destination Refused      | `INTERNAL_DESTINATION_REFUSED`      | `MCP server address resolves to an internal IP address, which the gateway forbids.`  |
| MCP Server           | Malformed Response                | `MALFORMED_RESPONSE`                | `MCP server response is not a valid JSON-RPC message.`                               |
| MCP Server           | Server JSON-RPC Error             | `JSONRPC_ERROR`                     | `MCP server returned a JSON-RPC error.`                                              |
| MCP Server           | Timeout                           | `TIMEOUT`                           | `MCP server did not respond in time.`                                                |
| MCP Server           | Capability Not Declared           | `CAPABILITY_NOT_DECLARED`           | `MCP server does not declare the capability the method requires.`                    |
| MCP Server           | Capability Not Implemented        | `CAPABILITY_NOT_IMPLEMENTED`        | `MCP server declares the capability but does not implement this method.`             |
| MCP Server           | Tool Execution Failure            | `TOOL_EXECUTION_FAILURE`            | `Tool call returned an execution error.`                                             |
| MCP Server           | Server Listen Failed              | `LISTEN_FAILED`                     | `Notification stream from the MCP server failed.`                                    |
| MCP Server           | Other Server Error                | `OTHER`                             | `MCP server request failed.`                                                         |
| Output Schema        | Schema Violation                  | `SCHEMA_VIOLATION`                  | `Tool result does not match the declared output schema.`                             |
| Tasks                | Tasks Not Declared                | `TASKS_NOT_DECLARED`                | `MCP client called a task method without declaring the tasks capability.`            |
| Tasks                | Unexpected Task Result            | `UNEXPECTED_TASK_RESULT`            | `MCP server returned a task result, but the MCP client does not support tasks.`      |
| Tool Filter          | Tool Not Invocable                | `TOOL_NOT_INVOCABLE`                | `Tool call was blocked by the tool filter policy.`                                   |
| Tool Filter          | Call Filter Failed                | `CALL_FILTER_FAILED`                | `Tool call was blocked: tool filter policy could not be evaluated.`                  |
| Tool Filter          | List Filter Failed                | `LIST_FILTER_FAILED`                | `Tool listing was blocked: tool filter policy could not be evaluated.`               |

## Related pages

* [MCP Access Exceptions](access-exceptions.md)
* [Supported protocols and application fields](../workload-events/supported-protocols.md)
