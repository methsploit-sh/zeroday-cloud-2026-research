# Candidate 004 — Snapshot GET (unauth) SQLi / data leak — REJECTED
## Hypothesis
GET /api/snapshots/:key is unauth. Could the key give SQLi, or read another org's snapshot?
## Runtime evidence
/api/snapshots/test -> 404 "Snapshot not found". Metrics: 0 snapshots present.
## Source evidence
dashboardsnapshots/database/database.go:94 GetDashboardSnapshot -> xorm sess.Get(&snapshot),
Key is parameterized (ORM). The handler enforces an org-mismatch check (OrgID != c.OrgID -> 401).
The dashboard is AES-encrypted (secretsService).
## Conclusion
Parameterized ORM (no SQLi), org-mismatch protection, and zero snapshots in the DB.
STATUS: REJECTED (parameterized ORM; org-gated; zero snapshots in DB)
