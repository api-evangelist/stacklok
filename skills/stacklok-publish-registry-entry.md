---
name: Publish and govern an MCP registry entry
description: Create a source and registry, publish an MCP server entry, and manage its claims via the ToolHive Registry Server admin API.
api: openapi/stacklok-registry-api-openapi.yml
operations:
  - "PUT /v1/sources/{name}"
  - "PUT /v1/registries/{name}"
  - "POST /v1/entries"
  - "GET /v1/entries/{type}/{name}/claims"
  - "PUT /v1/entries/{type}/{name}/claims"
---

# Publish and govern an MCP registry entry

Use the ToolHive Registry Server admin API (`/v1`) to curate which MCP servers and
skills are discoverable and who may access them.

## Auth
OAuth 2.0 / OIDC Bearer token required (`Authorization: Bearer {token}`); admin
operations are gated by JWT-claim role checks. See
`authentication/stacklok-authentication.yml`.

## Steps
1. **Define a source** — `PUT /v1/sources/{name}` (upsert) pointing at a Git, file, or
   API upstream that supplies entries.
2. **Create a registry** — `PUT /v1/registries/{name}` to combine sources into a catalog
   audience.
3. **Publish an entry** — `POST /v1/entries` with the server/skill payload. Expect `201`;
   `409` if that name/version already exists.
4. **Review ownership** — `GET /v1/entries/{type}/{name}/claims` to see current claims.
5. **Update access** — `PUT /v1/entries/{type}/{name}/claims` (`204`) to change claim-based
   visibility.

## Notes
`PUT` upserts are idempotent by resource name (no Idempotency-Key mechanism — see
`conventions/stacklok-conventions.yml`). Published entries are read back through the
MCP Registry API v0.1 (`/registry/{name}/v0.1/servers`).
