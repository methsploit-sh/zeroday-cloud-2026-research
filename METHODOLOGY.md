# Methodology

This repository documents a systematic search for remote code execution on two
ZeroDay.Cloud 2026 competition targets. The same method was applied to both.

## The candidate model

Every attack surface is treated as a numbered **candidate**. A candidate is not a
guess — it is a falsifiable claim examined against evidence and closed with a verdict.

Each candidate has four parts:

| Part | Question it answers |
|------|---------------------|
| **Hypothesis** | What is the theory — which surface, which sink, why it might be reachable? |
| **Runtime evidence** | What does the *live target* actually return when probed? |
| **Source evidence** | What does the *pinned source* of that exact version show at the relevant code path? |
| **Verdict** | Is it reachable, and if not, exactly what blocks it? |

A claim is only closed when the runtime behaviour and the source agree. A runtime
404 with no source reading is not a verdict — it is an untested assumption.

## Verdict labels

| Label | Meaning |
|-------|---------|
| **EXPLOITABLE** | A working, reachable path was demonstrated end to end. |
| **GATED** | A real sink exists, but reaching it requires a precondition the target does not provide (auth, a DB row, a registered server, pre-existing data). |
| **REJECTED** | The hypothesis does not hold — the path is closed by design, or the sink does not exist. |
| **INCONCLUSIVE** | The evidence available could not settle the question (e.g. a version diff too noisy to isolate a specific fix). |

The distinction between **GATED** and **REJECTED** matters. A gated finding is a
*real weakness* that is simply out of reach on the frozen challenge configuration;
it can become live in a different deployment. A rejected finding is closed on its
own merits.

## Why negative results are documented

Most of a real RCE hunt is elimination. A surface that is proven closed — with the
runtime probe and the source path that close it — is a result worth recording:

- it prevents re-checking the same dead ends,
- it narrows where an intended path could still hide,
- it is reproducible: anyone can re-run the probe and read the same source.

The two targets here were both found closed to every classic vector examined. That
is stated as a **well-evidenced negative**, not as "nothing found".

## Cross-checking

Selected high-value candidates (auth logic, the auth-fallback anomaly) were reviewed
in more than one independent pass to guard against a single reviewer's blind spot. A
candidate that survived every pass with the same verdict is marked accordingly.

## Scope and safety

- All probing was performed against a purpose-built competition target in an
  authorized environment.
- The pinned source of the exact target version was read locally; the live target
  was never modified.
- No working exploit for any production system is contained here.
