# Candidate 010 — Auth early-allow branches — REJECTED

## Challenge config (config.yaml) — MINIMAL
general_settings: only master_key: os.environ/LITELLM_MASTER_KEY
No optional settings at all (public_routes, pass_through_endpoints undefined).

## Three early-allow branches, all closed:
1. route_in_additonal_public_routes (auth_utils.py:614)
   -> if premium_user is not True: return False (line 639)
   No premium/enterprise license on the challenge + no public_routes in config. DEAD.
2. No-auth dev mode (user_api_key_auth.py:2471)
   -> if master_key is None and not(jwt/oauth) — master_key is SET. DEAD.
3. Pass-through auth:False (user_api_key_auth.py:771)
   -> returns UserAPIKeyAuth() ONLY if endpoint.get("auth") is not True
   pass_through_endpoints is NOT defined in config. DEAD.

## public_routes frozenset (_types.py:744) — hard-coded, clean
Only /, /health/*, /public/*, /test, /routes. NO mutation route.
mapped_pass_through_routes are provider forwarders; they do not pass without a key,
they only take the litellm_user_api_key header as api_key (a key is still required).

## master_key comparison — timing-safe
secrets.compare_digest (line 1843) + type check (line 1848). Safe.

STATUS: REJECTED (auth layer sound; minimal config leaves no bypass surface)
