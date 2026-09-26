# Candidate 017 — version-diff: DCR bridge credential-authority (327515a3ba) — REAL BUG, GATED

## Finding: fix 327515a3ba (#42563) is ABSENT from the target (verified at code level)
The _explicit_credential_matches_envelope function is absent from the target; the
DCR-bridge auth code is in its pre-fix state (user_api_key_auth_mcp.py:835
_admit_dcr_bridge_authorization).

## Weakness (present in the target)
Pre-fix: if a request carries an explicit x-litellm-api-key + a bridge envelope,
_admit_dcr_bridge_authorization sees the envelope and IGNORES the explicit key
(lines 844-846), admitting with the envelope's sealed identity. That identity may
differ from the explicit key's -> credential-confusion / scope-escalation.
The fix closes this with dual-credential + identity-match (mismatch=403).

## But unauth-untriggerable (007/016 pattern)
The exploit is gated in two layers:
1. The bridge envelope is SEALED with master_key (_open_dcr_bridge_envelope:872
   "if not master_key" + resolve_bridge_envelope(auth, keys, ...)). Without
   master_key the attacker cannot forge a valid envelope.
2. _single_dcr_bridge_delegate_target (769): requires an is_oauth_delegate +
   is_dcr_bridge server record. The challenge MCP hub is empty and there is no DB.

## Conclusion
A genuine credential-authority bug, present in the target in its pre-fix state.
But the exploit needs a master-key-sealed envelope + a registered DCR-bridge server,
neither of which an unauth attacker can supply on the minimal challenge config.

STATUS: REAL BUG but GATED (needs master-key-sealed envelope + DCR server;
unauth-untriggerable on the challenge config). The strongest version-diff finding.
