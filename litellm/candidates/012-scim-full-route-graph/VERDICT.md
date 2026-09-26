# Candidate 012 — SCIM + full route-graph — REJECTED

## Secondary-review note: "the 215 keyword-matrix is a spot-check, not a route-graph"
-> Addressed: ALL 495 mutation routes were probed unauth without keyword filtering.
   The 32 non-401 routes are all DB-missing (400/500/503), premium-gate (403),
   not-found (404), or the known 006/007 (200). No new exploitable route.

## SCIM (a new surface the keyword-matrix had missed) — dead behind a premium gate
scim_v2.py:261-264: APIRouter(prefix="/scim/v2",
                              dependencies=[Depends(_premium_user_check)])
The router-level premium check runs BEFORE auth (FastAPI router deps run first).

## Runtime proof (4 variants, ALL the same 403)
no key / master-key / GET / garbage-key -> all:
  403 "This feature is only available for LiteLLM Enterprise users"
=> _premium_user_check cuts every request before auth. With no LITELLM_LICENSE on
   the challenge, SCIM is entirely unreachable (even authenticated). The
   user_api_key_auth dependency is never reached. 403 = premium-fail, not auth-fail.

## Full route-graph summary
495 mutation routes probed unauth. The only unauth-200 are 006 (no-op stub,
handler-confirmed) + 007 (DCR-dummy, gated). Other non-401: DB-missing /
premium-gate / not-found — all dead.

STATUS: REJECTED (SCIM premium-gated pre-auth; the full route-graph confirms no new
unauth-exploitable surface. Secondary-review "spot-check" critique fully resolved.)
