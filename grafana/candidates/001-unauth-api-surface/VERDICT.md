# Candidate 001 — Full unauth API surface (OpenAPI-driven) — REJECTED
## Hypothesis
Could a hidden unauth /api endpoint reach a sink?
## Runtime evidence
143 GET + 81 POST endpoints were extracted from the OpenAPI spec and probed unauth.
GET: only 5 of 143 non-401 -> /health (200), /signing-keys/keys (200, public JWKS),
/public/dashboards/<uuid> (404, no public dashboards), /snapshots/<key> (404, no
snapshots). The remaining 138 -> 401.
POST: only /public/dashboards/.../query (404, no token) is non-401. The rest -> 401.
## Source evidence
pkg/api/api.go route registration: reqSignedIn/authorize middleware on every /api group.
With GF_AUTH_ANONYMOUS_ENABLED=false the context OrgID is 0, so every authed route is 401.
## Conclusion
The entire legacy + k8s (/apis) mutation/read surface is auth-gated. The unauth-reachable
endpoints return either information (health/metrics/jwks) or require pre-existing data
(public dashboard / snapshot — zero in the DB).
STATUS: REJECTED (unauth API surface uniformly auth-gated; no reachable sink)
