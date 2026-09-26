# Candidate 008 — Pre-auth parser primitive — REJECTED

## Hypothesis
user_api_key_auth reads the body before auth completes (OCR item 7: malformed
JSON -> 400 pre-auth). If that read chain contained a parser that deserializes
or executes, it would be an unauth primitive.

## Evidence (deterministic grep across all of litellm/proxy/auth/)
- Pre-auth body read: _read_request_body -> json.loads/orjson ONLY.
  _read_request_body_deferring_parse_failure (user_api_key_auth.py:1192)
  defers malformed JSON as a ProxyException (for tracing) — a parse error,
  not execution.
- yaml.load / pickle / marshal / eval / exec / xml / tarfile / zipfile:
  NONE present anywhere in the auth chain.
- The only importlib use: auth_utils.py:1483 importlib.util.find_spec(
  "onelogin.saml2.auth") — a constant string, only checks "is the module
  installed", NOT import/exec, NOT attacker-controlled.
- Multipart: auth_checks.py:737 / auth_utils.py:1641 only JSON-string ->
  dict coercion; no execution of file content.

## Conclusion
Pre-auth parsing is real but is only JSON deserialization (data, not code).
No execute / deserialize-to-object primitive.
=> No unauth pre-auth code-execution primitive.

STATUS: REJECTED (consistent with OCR item 7: pre-auth parse YES, exec NO)
