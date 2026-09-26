# Candidate 003 — Plugin static path traversal — REJECTED
## Hypothesis
/public/plugins/:pluginId/* is unauth; could path traversal read /flag.sh or config?
## Runtime evidence
public/plugins/dashboard/../../../../etc/passwd + %2e%2e + ..%2f + ....// variants
-> all 404. Even a core plugin asset (fav32.png) is 404 (the plugin FS is minimal).
## Source evidence
pkg/api/plugins.go getPluginAssets: pluginStore.Plugin() is an exact-match map lookup
(no path), sanitized via plugins.CleanRelativePath(). Plugin install/unsigned is DISABLED
on the challenge. No plugin symlink exists under public/ (find -type l is empty).
## Conclusion
CleanRelativePath blocks traversal, the plugin store is a map lookup, and there are no symlinks.
STATUS: REJECTED (CleanRelativePath sanitizes; no plugins loaded; no symlinks)
