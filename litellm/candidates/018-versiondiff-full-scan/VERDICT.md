# Candidate 018 — version-diff full scan — NO UNAUTH 0-DAY

## Scope: 355 post-target (after 2026-09-22) security-keyword commits
All auth/security/bypass/credential/token/permission/scope commits on origin/main
after the target date were classified.

## Result
- Unauth-themed commits: 0  (no fix is unauth / pre-auth / public / anonymous)
- Auth-gated (authenticated scenario): 24 — all credential/token/permission/scope,
  i.e. authorization issues that require a VALID identity
- Other: ~330 (logging, streaming, tests, refactor, provider features)

## Assessment
Post-target LiteLLM patched NO unauth vulnerability. Every auth fix is authenticated
(the 007/016/017 pattern: real bugs but master_key/DB/server-gated).
017 (DCR credential authority) is the strongest finding: a real bug, present in the
target in its pre-fix state, but requiring a master-key-sealed envelope + DCR server.

## Competition angle
The pre-auth RCE the challenge asks for is NOT in the version-diff. The post-target
patches fix weaknesses that are unauth-untriggerable on the minimal challenge config.

STATUS: NO UNAUTH-EXPLOITABLE 0-DAY in the version-diff. Auth surface hardened;
all post-target auth fixes are authenticated-scenario (gated on the challenge config).
