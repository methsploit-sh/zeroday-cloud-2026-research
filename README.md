# ZeroDay.Cloud 2026 — Security Research

Systematic pre-authentication and authenticated RCE research against two
ZeroDay.Cloud 2026 competition targets, documented as reproducible candidate
analyses with runtime + source evidence.

## Targets
- **LiteLLM** v1.102.1 — unauthenticated pre-auth RCE ($40,000)
- **Grafana** 13.2.2 — unauthenticated pre-auth RCE ($40,000) / authenticated RCE ($10,000)

## Methodology
Each attack surface was examined as a numbered *candidate*: a hypothesis, a
runtime probe against the live target, corresponding source-code analysis of the
pinned version, and a verdict (EXPLOITABLE / GATED / REJECTED / INCONCLUSIVE).

## Result
Both targets, in their minimal competition configuration, were found closed to
every classic unauth-RCE vector examined — a well-evidenced negative result.
See each target's `MASTER_SUMMARY.md` and `candidates/` directory.
