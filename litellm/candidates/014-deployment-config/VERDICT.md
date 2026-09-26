# Candidate 014 — Deployment / compose config — REJECTED

## Full deployment read (docker-compose + local-isolation + config.yaml)
LiteLLM env: ONLY LITELLM_MASTER_KEY + MOCK_OPENAI_API_KEY.
  -> LITELLM_WORKER_STARTUP_HOOKS is ABSENT (the 011 exec primitive is not triggered)
command: standard (--config --port 4000 --num_workers 1), no entrypoint override
local-isolation: !override 127.0.0.1:4000:4000 -> port bound to localhost only
mock-openai: expose 8080 (internal compose network only), NOT published to the host
config.yaml: model zdc-mock + master_key + mock api_key. No dangerous setting.

## Conclusion
The deployment is minimal and hardened. What the attacker sees: a master_key-protected
LiteLLM @ 127.0.0.1:4000. The mock is unreachable, no dangerous env, no startup hook.

STATUS: REJECTED (deployment surface clean; no dangerous env/port/hook/command)
