# Candidate 007 — Route normalization auth bypass (302 vs 401) — REJECTED
## Hypothesis
A "302 vs 401 auth-layer difference" hint. Do //api, /api/./, %2f, ..; variants reach an
authed endpoint unauth?
## Runtime evidence
/api/datasources -> 401. //api/datasources, /api/./datasources, /api%2fdatasources,
/api/..;/datasources -> all 302 (login redirect, NOT a bypass — Location=/login).
/apis/* (k8s) -> all 401. No variant reached an authed handler.
## Source evidence
The Go mux does not normalize; /api/./x does not match a route -> catch-all -> 302 login.
302 = "rejected (web redirect)", 401 = "rejected (API JSON)". Both are rejections.
## Conclusion
The 302/401 difference is only redirect-vs-JSON rejection style; not a bypass.
STATUS: REJECTED (normalization variants redirect to login; no handler reached unauth)
