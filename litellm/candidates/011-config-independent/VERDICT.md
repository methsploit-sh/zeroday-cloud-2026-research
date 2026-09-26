# Candidate 011 — Config-independent paths (env interp, startup hooks, middleware) — REJECTED

## os.environ/ interpolation — eliminated
All uses are config-load/integration (redis, s3, qdrant, slack).
get_secret calls use constant strings. No path injects os.environ/X from the request body.

## Startup hook exec — REAL primitive but NOT unauth
proxy_server.py:1107  os.environ.get("LITELLM_WORKER_STARTUP_HOOKS")
  -> importlib.import_module(module_path); getattr(func); _hook_fn()  [EXEC]
Source: an environment variable, NOT an HTTP request. The attacker cannot set env.
This env var is absent from the challenge config. Startup-time, operator-only.

## Middleware chain (pre-auth, config-independent) — CLEAN
CORS / BillableMetrics / InFlightRequests / SecurityHeaders: do not process the body.
PrometheusAuthMiddleware (middleware/prometheus_auth_middleware.py):
  - Applied only to the /metrics endpoint (line 41: if not metrics: return)
  - require_auth_for_metrics_endpoint is not False -> CALLS user_api_key_auth()
  It has no weak auth of its own; it runs the real auth.

STATUS: REJECTED (config-independent surface also clean; middleware does not exec/deserialize the body)
