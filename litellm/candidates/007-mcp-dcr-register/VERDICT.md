# Candidate 007 — MCP DCR /register unauth SSRF — REJECTED

## Sink (real)
_post_dcr_registration (discoverable_endpoints.py:1606) does
async_client.post(registration_url, json=register_data) — a genuine
outbound HTTP POST to a URL from mcp_server.effective_registration_url.

## Why unreachable on the challenge config
- register_client_with_server reaches the sink ONLY if
  resolved_server.effective_registration_url is non-None (lines 1806-1807);
  otherwise `return dummy_return`.
- effective_registration_url comes from an MCP server record (DB/config).
- The challenge has NO Prisma DB and an EMPTY MCP hub (/public/mcp_hub -> []).
- Named path {name}/register: registry lookup -> None -> dummy_return.
- Root /register: _resolve_oauth2_server_for_root_endpoints requires
  exactly ONE configured oauth2 server; there are ZERO -> None.

## Runtime proof
- POST /{test,x,foo}/register  -> 200 dummy_client/dummy (no sink)
- POST /register {}            -> 200 dummy_client/dummy (no sink)
- GET  /authorize?...          -> 404 "MCP server not found"
- POST /register redirect_uris=http://169.254.169.254/ -> 400 invalid_redirect_uri
  (redirect_uri SSRF also blocked: https / loopback-http / registered native only)

## Prerequisite to reach the sink
An OAuth2 MCP server must first be registered with a registration_url set.
Server registration (/v1/mcp/server) requires auth (401 unauth).
=> No unauth path to the SSRF sink on this exact target.

STATUS: REJECTED (sink real, unauth source not found — same pattern as 001-005)
