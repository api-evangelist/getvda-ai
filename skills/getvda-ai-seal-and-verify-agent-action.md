---
generated: '2026-09-19'
method: generated
name: getvda-ai-seal-and-verify-agent-action
description: Mint a free Sealed-tier Witness key in-band, seal an autonomous agent action under a governing rule, read it back, and verify the chain offline with zero Witness calls.
api: openapi/getvda-ai-witness-openapi.json
operations: ['POST /api/witness/test-key', 'POST /api/witness/seal/agent-action', 'GET /api/witness/records/{recordId}', 'GET /api/witness/chains/{chainKey}/proof', 'POST /api/witness/verify']
source: >-
  Grounded in openapi/getvda-ai-witness-openapi.json (the provider declares operationIds on only two operations,
  so routes are named by method + path) and https://witness.getvda.ai/llms.txt. The same flow is available over
  MCP as get_test_key -> seal_agent_action -> get_record -> verify (mcp/getvda-ai-mcp.yml).
---

# Seal and verify an agent action

Record that an agent took an action under a named rule, in a form an auditor can reconstruct, then prove the
record and its chain are intact without trusting Witness.

## Auth
- `POST /api/witness/test-key` with `{}` (or `{"controllerPublicKeyJwk": {...}}` for a durable account) returns
  `apiKey` (`wtn.<id>.<secret>`) and `accountId`. No human, no signup. Quick-start keys expire in 7 days.
- Send `Authorization: Bearer <apiKey>` on every write. Never send an `account` field — it is derived from the key (400 if you do).
- See `authentication/getvda-ai-authentication.yml`.

## Idempotency and grouping
- `seal/agent-action` has NO idempotency key; do not blind-retry a timed-out call (see `conventions/getvda-ai-conventions.yml`).
- Put every seal of one workflow on one `chainKey` (`"acp:run:<id>"` style, max 128 chars `[A-Za-z0-9._:-]`). Do not build your own manifest record — the chain link is the grouping proof.

## Steps
1. **Mint a key** — `POST /api/witness/test-key`. Keep `accountId`; the account is the durable identity, the key is not.
2. **Seal the action** — `POST /api/witness/seal/agent-action` with `actor {id, type: agent}`, `action {statement, outcome?}`, `governing_rule {ref, text?}`, and at least one of `evidence[{ref, hash}]` (external material, hashed) or `parameters {...}` (computed inputs) — else `evidence_omitted_reason`. Optional `chainKey`. Capture `recordId`.
3. **Read it back** — `GET /api/witness/records/{recordId}` returns the full signed body (decision, governingRule, prevHash, proof) plus `anchorState`.
4. **Fetch the offline proof** — `GET /api/witness/chains/{chainKey}/proof` (or `GET /api/witness/records/{recordId}?proof=chain`) returns record + chain + anchor + did.json in one call.
5. **Verify** — offline with `vda-witness` (`offlineVerify({record, chain, didDocument, anchor})` -> `ANCHORED_VALID | SIGNED_PENDING | BROKEN | INSUFFICIENT_PROOF`), or `POST /api/witness/verify {"record": ...}` (public, no key) -> `{ok, bodyHash, signatureValid}`.

## Errors
- `401 {"error":"unknown or invalid API key"}` — missing/expired/revoked key; renew via `renew/challenge` + `renew`.
- `413` — inline evidence over 100 KB; reference it by `{ref, hash}` instead.
- `429` — per-account token bucket (default 120/min); no Retry-After header. Catalog in `errors/getvda-ai-problem-types.yml`.

## Notes
- Sealed-tier records are signed and chain-verifiable but NEVER externally anchored; `SIGNED_PENDING` is the terminal state on the free tier. "Provable even against VDA" needs the Anchored tier.
- A seal proves "at time T this account asserted X", not that X is true.
