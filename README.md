# ZeroDay.Cloud 2026 — Security Research

![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-blue.svg)
![Type](https://img.shields.io/badge/type-security%20research-red)
![Targets](https://img.shields.io/badge/targets-2-informational)
![Candidates](https://img.shields.io/badge/candidates-28-orange)
![Result](https://img.shields.io/badge/result-well--evidenced%20negative-lightgrey)

Systematic pre-authentication and authenticated RCE research against two
ZeroDay.Cloud 2026 competition targets, documented as reproducible **candidate**
analyses backed by both runtime and source evidence.

---

## Targets

| Target | Version | Prize scope | Candidates | Result |
|--------|---------|-------------|:----------:|--------|
| **LiteLLM** | v1.102.1 | Unauth pre-auth RCE ($40,000) | 18 | No RCE — well-evidenced negative |
| **Grafana** | 13.2.2 | Unauth pre-auth ($40k) / auth RCE ($10k) | 10 | No RCE — one real, unchainable SSRF |

---

## Methodology

Each attack surface is examined as a numbered **candidate**: a hypothesis, a runtime
probe against the live target, source-code analysis of the pinned version, and a
verdict. Full detail in **[METHODOLOGY.md](METHODOLOGY.md)**.

| Verdict | Meaning |
|---------|---------|
| `EXPLOITABLE` | Reachable, demonstrated end to end |
| `GATED` | Real sink, but blocked by a missing precondition (auth / DB / data) |
| `REJECTED` | Hypothesis does not hold; path closed by design |
| `INCONCLUSIVE` | Evidence could not settle the question |

---

## Key findings

**LiteLLM** — the auth / MCP / DCR surface contains *real* weaknesses (candidate 017,
a DCR credential-authority bug, is genuinely present in the target), but every one is
gated by `master_key`, a database, or a registered MCP/OAuth server — none of which the
minimal challenge configuration provides. All 495 mutation routes were probed unauth;
only two return 200, both inert.

**Grafana** — the entire `/api` and `/apis` surface is auth-gated. Provisioning was the
most promising authenticated surface, but local-repo write is closed by three independent
layers (API 403 + read-only filesystem + no datasource sync). The one genuine finding is a
weak Git-URL allowlist (SSRF), which cannot be chained to RCE in the challenge topology.

---

## Layout

```
.
├── litellm/
│   ├── MASTER_SUMMARY.md
│   └── candidates/006-018/…/VERDICT.md
├── grafana/
│   ├── MASTER_SUMMARY.md
│   └── candidates/001-010/…/VERDICT.md
├── METHODOLOGY.md
└── LICENSE
```

Start with each target's **`MASTER_SUMMARY.md`**, then drill into individual
`candidates/*/VERDICT.md` files.

---

## Result

Both targets, in their minimal competition configuration, were found closed to every
classic RCE vector examined — a **well-evidenced negative result**. Each real weakness
that was found is a *gated* one: out of reach on the frozen challenge config, but
recorded because it could matter in a different deployment.

---

## Scope & safety

All probing was performed against purpose-built competition targets in an authorized
environment. The pinned source of each target version was read locally; the live target
was never modified. This repository contains analysis and negative findings only — no
working exploit for any production system.

---

## License

Documentation licensed under **[CC BY 4.0](LICENSE)**. Attribution: `methsploit-sh`.
