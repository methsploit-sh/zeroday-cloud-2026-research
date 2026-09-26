# Candidate 010 — Authenticated: provisioning Git-URL SSRF — REAL SSRF, no RCE chain
## Finding (genuine)
isValidGitURL (repository.go:176) checks only scheme (http/https) + host + path;
localhost / internal-IP / metadata endpoints ALL pass. allowed_git_urls is empty (no
restriction). The Git client is nanogit (not go-git) -> no argument-injection, only HTTP SSRF.
## Why it is not RCE
SSRF alone is not RCE. There is no internal RCE-service to reach in the Grafana container
(the flag is a static file, no other service is running). SSRF only has value if chained to
an internal sink; none exists in this challenge topology.
## Assessment
A genuine authenticated SSRF (weak URL allowlist), but it cannot be chained to RCE in this
deployment. It could be reported to Grafana's bug-bounty as an SSRF (not RCE, low severity).
STATUS: REAL SSRF but no RCE chain in this deployment (no internal target service)
