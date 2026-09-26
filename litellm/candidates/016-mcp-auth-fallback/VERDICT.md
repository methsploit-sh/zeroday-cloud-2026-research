# Candidate 016 — MCP _admission_failure_fallback auth anomaly — REJECTED (not a bypass)

## Runtime evidence (3-way distinction)
POST /mcp  {"jsonrpc":"2.0","method":"tools/list","id":1}  Accept: json+sse
- no Authorization header  -> 401 "authentication_required"
- invalid Bearer token     -> 400 "No connected db"   <-- NOT 401!
- master-key               -> 200 {"tools":[]}

## Why this looked interesting
no-header is REJECTED (401) but invalid-token is NOT (400). The invalid-token
request progresses PAST the auth 401 into a DB-requiring handler. This matches
_admission_failure_fallback (user_api_key_auth_mcp.py:255): a genuine 401 from
user_api_key_auth is swallowed and an anonymous UserAPIKeyAuth() is returned
(cold-start passthrough, line 286).

## Resolution
The anonymous admit always hits the DB-backed permission wall; the 400 is the
anonymous ceiling, not a near-miss. The fallback is real but inert without a DB —
the 400 is a DB-lookup artifact, not an auth bypass. With no DB on the challenge,
there is no reachable sink.

STATUS: REJECTED (auth-fallback is real but inert; 400 is a DB-lookup artifact, not a bypass)
