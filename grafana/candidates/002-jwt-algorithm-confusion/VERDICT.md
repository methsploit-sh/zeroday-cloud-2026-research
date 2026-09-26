# Candidate 002 — JWT algorithm confusion (JWKS public key) — REJECTED
## Hypothesis
/api/signing-keys/keys returns an unauth JWKS (EC public key). Could algorithm confusion
(alg:none, or HS256-with-pubkey) yield an auth bypass?
## Runtime evidence
An alg:none forged JWT against /api/user, /api/search -> 401 "Invalid API key".
## Source evidence
pkg/services/auth/jwt/auth.go: Verify() parses with go-jose; the keySet is an EC public key.
go-jose rejects an HMAC token signed with the EC key on a type mismatch.
conf/defaults.ini [auth.jwt] enabled=false -> JWT auth is disabled anyway.
signingkeysimpl/service.go: the JWKS exposes only x/y (public); the private 'd' is absent.
## Conclusion
JWT auth disabled + go-jose type-checking + public key only -> algorithm confusion is
impossible. The JWKS is public by design.
STATUS: REJECTED (JWT disabled; go-jose type-checks prevent confusion; pubkey only)
