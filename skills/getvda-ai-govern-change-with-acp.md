---
generated: '2026-09-19'
method: generated
name: getvda-ai-govern-change-with-acp
description: Take a governance change through ACP — load the current set, prepare and Compliance-Guard the diff, run the CI test suite, surface it for a non-engineer approver, record the decision, activate the signed bundle, and let agents fetch it.
api: openapi/getvda-ai-acp-openapi.json
operations: ['GET /v1/environments/{env}/governance', 'POST /v1/environments/{env}/changes/prepare', 'POST /v1/environments/{env}/changes/test', 'GET /v1/environments/{env}/pulls/{num}/surface', 'POST /v1/environments/{env}/pulls/{num}/decision', 'POST /v1/environments/{env}/versions/activate', 'GET /bundles/{env}/active.json', 'GET /bundles/{env}/versions/{version}']
source: >-
  Grounded in openapi/getvda-ai-acp-openapi.json (no operationIds declared; routes named by method + path), the
  served ACP agent card (skills, contracts, boundaries) and the @getvda/* package descriptions in
  packages/getvda-ai-packages.yml. Live GET https://acp.getvda.ai/readyz on 2026-09-19 reported git, witness,
  bundle and bundleStore live and identity DISABLED — step 5's OIDC approver credential is staged, not live.
---

# Govern a change with ACP

Git holds the rules; ACP is the governance-aware wrapper on it. Nothing here asks agents to git-pull — they
fetch signed bundles.

## Auth
- `Authorization: Bearer wtn.<keyId>.<secret>` (Witness key, validated via whoami) on every `/v1/` route. Bundle routes are public.
- `X-Approver-Credential: <OIDC token>` identifies the human approver on the decision route — declared in the spec, currently `identity: disabled` on the live service.

## Steps
1. **Load** — `GET /v1/environments/{env}/governance?ref=...` returns the parsed governance set at a git ref.
2. **Prepare** — `POST /v1/environments/{env}/changes/prepare` validates a proposed edit: structural diff + Compliance Guard (weakening detection — MUST counts, citations, required refs, conditioned capabilities). Writes nothing.
3. **Test** — `POST /v1/environments/{env}/changes/test` runs the governance CI suite (schema, guard, adversarial scenarios, deterministic impact analysis). The same suite ships as `@getvda/test-suite` for your own CI.
4. **Surface** — `GET /v1/environments/{env}/pulls/{num}/surface` renders the plain-language change, test results and impact for a non-engineer approver.
5. **Decide** — `POST /v1/environments/{env}/pulls/{num}/decision`: approve/deny with federated identity + git authority (CODEOWNERS + branch protection) + separation of duties (`409` if the approver authored the change). Approve seals a `hitl_decision` to Witness and merges.
6. **Activate** — `POST /v1/environments/{env}/versions/activate` builds a deterministic, content-hashed, Ed25519-signed bundle (`acp.bundle/1`, key `did:web:acp.getvda.ai#key-3`) and seals the activation to Witness against the commit hash. Rollback = activate the previous version.
7. **Distribute** — agents fetch `GET /bundles/{env}/active.json` (or a pinned `GET /bundles/{env}/versions/{version}`), verify the signature against the ACP DID document, and evaluate locally with `@getvda/evaluator-sdk` / `getvda-evaluator` (fail-static: if governance cannot be verified the action is refused).

## Errors
- `401 identity`, `403 authority`, `409 separation of duties` on decision; `404 no_active_bundle` when nothing is active (observed live for `default`); `503 git staged` / `bundle/witness staged` while a capability is not yet live.

## Notes
- ACP verifies the STRUCTURE of governance, not that a natural-language condition was met — the agent's assertion is sealed, making it accountable.
- ACP does not generate genesis governance (that is C2MD) and does not own identity (federated from the customer IdP).
