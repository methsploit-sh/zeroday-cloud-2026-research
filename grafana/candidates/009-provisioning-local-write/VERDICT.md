# Candidate 009 — Authenticated: provisioning local-repo write -> RCE — REJECTED
## Hypothesis
Can an admin write a datasource/plugin YAML via a local provisioning repository
(conf/provisioning is a permitted prefix) and reach RCE?
## Runtime evidence (admin credentials)
- Repository create: type=local path=conf/provisioning -> 200 ACCEPTED (permitted prefix)
- type=local path=/etc -> 403 "matches no permitted prefix" (traversal closed)
- files subresource POST (write) -> 403 "write operations are not allowed for this repository"
- docker exec test -w conf/provisioning/datasources -> readonly (FS-level read-only)
## Source evidence
local/local.go: safepath.Join/Clean/InDir + PermittedPrefixes block traversal.
A Write/os.WriteFile method exists, BUT the API blocks write for a local repo with 403.
permitted_provisioning_paths = devenv/dev-dashboards|conf/provisioning (narrow).
provisioning resources = folder|dashboard|libpanel|playlist (NO DATASOURCE).
## Conclusion — three independent layers closed
(1) API local-repo write 403, (2) FS directory readonly, (3) provisioning does not sync datasources.
STATUS: REJECTED (local-repo write API-blocked + FS readonly + no datasource provisioning)
