---
name: Discover and run an MCP server with ToolHive
description: Search the ToolHive registry for an MCP server, inspect it, then create and manage a running workload via the ToolHive API.
api: openapi/stacklok-toolhive-api-openapi.yml
operations:
  - "GET /api/v1beta/registry/{name}/servers"
  - "GET /api/v1beta/registry/{name}/servers/{serverName}"
  - "POST /api/v1beta/workloads"
  - "GET /api/v1beta/workloads/{name}/status"
  - "POST /api/v1beta/workloads/{name}/stop"
---

# Discover and run an MCP server with ToolHive

Use the ToolHive API server (started with `thv serve`) to find an MCP server in a
registry and run it as a governed workload.

## Auth
The Registry API requires an OAuth 2.0 / OIDC Bearer token
(`Authorization: Bearer {token}`); the local `thv serve` API may run in anonymous
mode for development. See `authentication/stacklok-authentication.yml`.

## Steps
1. **List available servers** — `GET /api/v1beta/registry/{name}/servers` to browse the
   configured registry (cursor pagination: `cursor`, `limit`).
2. **Inspect a server** — `GET /api/v1beta/registry/{name}/servers/{serverName}` to read
   its metadata, transport, and required inputs.
3. **Run it as a workload** — `POST /api/v1beta/workloads` with the server image and
   run configuration. Expect `201`; handle `409` if a workload of that name exists.
4. **Check status** — `GET /api/v1beta/workloads/{name}/status` until healthy.
5. **Stop when done** — `POST /api/v1beta/workloads/{name}/stop` (async, `202`).

## Error handling
Errors return a JSON `ErrorResponse` (`error`, `message`) — not RFC 9457. See
`errors/stacklok-problem-types.yml`. Retry `500/503`; fix payload on `400`; the
workload name must be unique (`409`).
