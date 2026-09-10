---
type: definition
title: "Client ID Metadata Document"
description: "A JSON document that an MCP client hosts at an HTTPS URL, containing its client_id, client_name, and redirect_uris. When a client presents that URL as its OAuth client_id, the Authorization Server fetches and validates the document, so the client can authenticate without Dynamic Client Registration (DCR)."
tags: ["authentication"]
timestamp: 2026-06-30T15:16:59-04:00
---

# Client ID Metadata Document

A JSON document that an MCP client hosts at an HTTPS URL, containing its client_id, client_name, and redirect_uris. When a client presents that URL as its OAuth client_id, the Authorization Server fetches and validates the document, so the client can authenticate without Dynamic Client Registration (DCR).

Learn more: [Client ID Metadata Document](https://modelcontextprotocol.io/specification/2025-11-25/basic/authorization#client-id-metadata-documents)

**Related terms:** [Dynamic Client Registration](dynamic-client-registration.md), [MCP Authorization Server](mcp-authorization-server.md), [MCP Client](mcp-client.md)
