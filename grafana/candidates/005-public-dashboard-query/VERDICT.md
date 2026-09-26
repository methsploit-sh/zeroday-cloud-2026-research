# Candidate 005 — Public dashboard unauth query -> datasource — GATED
## Hypothesis
POST /api/public/dashboards/:token/panels/:id/query runs an unauth datasource query;
could it reach SSRF/RCE?
## Runtime evidence
/api/public/dashboards/<uuid>/panels/1/query -> 404 "Dashboard not found".
Metrics: grafana_stat_totals_public_dashboard=0, publicDashboardsEnabled=true but zero dashboards.
## Source evidence
publicdashboards/internal/api/api.go: the query route validates via IsValidAccessToken + a DB
lookup. Without a valid, published public dashboard it returns 404.
## Conclusion
The mechanism for an unauth datasource query exists, but it requires a published public
dashboard; the DB has zero. It is unreachable without pre-existing data (which cannot be
manufactured unauth).
STATUS: GATED (needs a pre-published public dashboard; none exist; cannot be created unauth)
