# Candidate 013 — Mock response-parsing surface — REJECTED (unauth-unreachable)

## Hypothesis: is there a primitive in LiteLLM's mock-backend response parsing,
## and can an unauth request trigger it?

## Runtime: ALL LLM-API routes are unauth 401
/v1/chat/completions, /chat/completions, /v1/completions, /v1/embeddings,
/v1/messages, /v1/responses -> all 401 "No api key passed in"

## Config clarified
model_list: zdc-mock -> openai/zdc-mock, api_base http://mock-openai:8080/v1
One model, mock backend, master_key. (An authenticated request must use model zdc-mock.)

## Conclusion
Reaching the response-parsing surface requires auth. An unauth attacker cannot
send a request to the mock model, so the response-parsing path cannot be triggered. Closed.

STATUS: REJECTED (LLM-API surface uniformly auth-gated; response-parsing unauth-unreachable)
