---
generated: '2026-09-19'
method: generated
name: getvda-ai-raise-and-resolve-hitl-decision
description: Register an authority config with HITL, raise an escalated agent decision for a human, read it, record the human's resolution (sealed to Witness), and check remaining quota.
api: openapi/getvda-ai-hitl-openapi.json
operations: [register_authority_config, get_raise_quota, raise_hitl_item, get_hitl_item, resolve_hitl_item, list_hitl_items]
source: >-
  Grounded in openapi/getvda-ai-hitl-openapi.json (operationIds verified) and the served HITL agent card's
  skills[].inputSchema and caveats. The live MCP surface at https://hitl.getvda.ai/mcp is auth-gated (401 on
  initialize) so tool schemas come from the card, not a live tools/list.
---

# Raise and resolve a human-in-the-loop decision

HITL owns human decisions on agent actions: the deployment decides WHEN to escalate; HITL routes, records and
seals. It performs no writes in any downstream system.

## Auth
- Every call (REST `/v1/tools/*` and `POST /mcp`, initialize included) needs `Authorization: Bearer wtn.<keyId>.<secret>` — a Witness key HITL validates via Witness `/whoami` (Contract A). Header only, never body or query.
- A NEW caller is created only by controller-signed genesis (`genesis_authority_config`, 403 `invalid_signature` / 409 `already_genesised`); `register_authority_config` updates an existing caller.

## Steps
1. **Register authority** — `POST /v1/tools/register_authority_config` (`register_authority_config`) with the config governance ACTIVATED: `bands[]` (narrowest -> widest, order is the escalation path), `label_to_band`, `roster` (band -> actor ids; bands nest), `permitted_decision_classes[]`, `max_raises_per_hour`, `max_open_items`. Git remains the system of record; HITL holds a projection.
2. **Check headroom** — `POST /v1/tools/get_raise_quota` (`get_raise_quota`): remaining raises this hour, open-item headroom, permitted classes. Read-only.
3. **Raise** — `POST /v1/tools/raise_hitl_item` (`raise_hitl_item`) with the decision statement, evidence as content addresses (`ref` + digest — HITL never fetches evidence) and the escalation label; HITL routes to the authorised band.
4. **Read** — `POST /v1/tools/get_hitl_item` (`get_hitl_item`) for the full item; `list_hitl_items` returns summaries only (no evidence, statement or domain_ref by design).
5. **Resolve** — `POST /v1/tools/resolve_hitl_item` (`resolve_hitl_item`) with the outcome and the ASSERTED human actor. The seal is durably queued before the call returns; the asserted actor and the authenticated calling account are recorded as separate fields. The deployment then executes the outcome itself.

## Errors
- `401 {"error":"unauthorized","message":"missing bearer credential"}`; `403` (authority / signature); `409` (quota, already genesised); `503` when a gating probe (witness / store / issuer identity) is not live — poll `GET /readyz`, which reports each check with latency.

## Notes
- HITL records who the caller SAYS decided; it does not authenticate the human — session integrity is the deployment's responsibility (card caveat `actor_asserted_not_authenticated`).
- Baseline promotions are privilege-widening events sealed customer-managed (`did:web:hitl.getvda.ai#key-2`); `revoke_baseline` is never a delete.
