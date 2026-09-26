# Grafana — ZeroDay.Cloud 2026 — Master Summary
Target: Grafana 13.2.2 @ 1bea008f7e4e858b6824c9e364d608bd4d10b13a
Config: GF_AUTH_ANONYMOUS_ENABLED=false, plugin install/preinstall/unsigned disabled, port 3000
Method: OpenAPI-driven full unauth probe + independent dual review, each candidate
verified against both the live target and the pinned source.

## Pre-auth candidates ($40,000 target)
001 unauth-api-surface      REJECTED     — 143 GET + 81 POST OpenAPI, all 401 (5 info/empty exceptions)
002 jwt-algorithm-confusion REJECTED     — JWT disabled, go-jose type-check, pubkey-only JWKS
003 plugin-path-traversal   REJECTED     — CleanRelativePath, no plugins loaded, no symlinks
004 snapshot-sqli/orgmatch  REJECTED     — parameterized ORM, org-gated, 0 snapshots
005 public-dashboard-query  GATED        — needs a pre-published public dashboard; DB has 0
006 metrics-info-leak       REJECTED     — operational metrics only, no secrets, not an RCE
007 auth-route-normalization REJECTED    — //api, %2f, ..; -> 302 login (not a bypass)
008 versiondiff-post-13.2.2 INCONCLUSIVE — main too divergent for a clean diff; known CVEs patched

## Authenticated candidates ($10,000 target, admin credentials provided)
009 provisioning-local-write REJECTED    — 3 layers: write API 403 + FS readonly + no datasource sync
010 provisioning-git-ssrf    REAL SSRF, no RCE chain — weak URL allowlist, but no internal target to reach

## Full unauth surface (complete)
/health (200), /metrics (200), /signing-keys/keys (200, public JWKS),
/public/dashboards/* (404, 0 dashboards), /snapshots/:key (404, 0 snapshots),
/login /signup /bootdata (HTML/info), /api/user/{signup,invite,password} (POST, gated/no-sink).
The entire /api and /apis surface is auth-gated; unauth-reachable endpoints return only
information or require pre-existing data.

## Conclusion (pre-auth + authenticated)
On the frozen challenge target (minimal config: anonymous disabled, plugins disabled, zero
snapshots / public dashboards in the DB), every classic RCE vector examined is closed —
both the $40,000 pre-auth path and the $10,000 authenticated path. Provisioning was the most
promising authenticated surface (conf/provisioning is a permitted prefix, a Write method
exists, the Git-URL allowlist is weak), but provisioning-write is closed by three independent
layers, and the one genuine finding (Git-URL SSRF) cannot be chained to RCE in this
container topology.

The only reportable artifact is the authenticated Git-URL SSRF (weak allowlist), which is a
low-severity finding against Grafana's own bug-bounty rather than a competition RCE.

STATUS: NO RCE (pre-auth or authenticated) via the examined vectors. Well-evidenced negative.
