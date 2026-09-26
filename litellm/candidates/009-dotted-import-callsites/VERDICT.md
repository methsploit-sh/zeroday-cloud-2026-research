# Candidate 009 — get_instance_fn dotted-import unauth reachability — REJECTED

## Sink (real, guard-free)
types_utils/utils.py:52  else: importlib.import_module(module_name)
The dotted-import branch has NO config_file_path guard (unlike the s3/gcs branch).
getattr(module, instance_name) returns a callable.

## Reachability verdict: NO (4 independent confirmations)
1. Deterministic grep: 19/19 call-sites pass config_file_path; NONE call with
   config_file_path=None. All are fed by general_settings/litellm_settings (YAML).
2. Independent review pass: UNAUTH_REACHABLE=NO
3. Second independent review pass: UNAUTH_REACHABLE=NO (explicit)
4. Manual source reading: same conclusion.

## Noted nuance (low priority)
An E001 comment implies an admin-endpoint request-body flow where
config_file_path=None (for the s3/gcs guard rationale). Possibly
guardrail_registry.py:616 or callback_utils.py:390/409 (3 truncated call-sites).
IF a runtime guardrail/callback-add path called get_instance_fn with
config_file_path=None, the dotted branch would be reachable — BUT that path
requires admin auth (master_key) and likely a DB (absent on the challenge). NOT unauth.

## Follow-up — RESOLVED
The 3 truncated call-sites were reviewed; all require config_file_path:
- guardrail_registry.py:616 -> preceded by `if not config_file_path: raise Exception`
  (get_instance_fn is UNREACHABLE without config_file_path)
- callback_utils.py:390/409 -> initialize_callbacks_on_proxy signature is
  `config_file_path: str` (required, no None default)
No caller anywhere passes config_file_path=None.
=> The runtime-None speculation is disproven. 009 is definitively closed.
5 independent proofs: grep + two independent review passes + manual + follow-up.

STATUS: REJECTED (sink real, unauth source NOT found — 9th consecutive gated candidate)
