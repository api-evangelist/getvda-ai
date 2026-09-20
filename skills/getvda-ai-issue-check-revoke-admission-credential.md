---
generated: '2026-09-19'
method: generated
name: getvda-ai-issue-check-revoke-admission-credential
description: Issue a Witness-sealed admission credential for an agent DID, verify it publicly as an enforcer (with the issuer-authenticity signal), and revoke it as the issuer.
api: openapi/getvda-ai-witness-openapi.json
operations: ['POST /api/witness/credentials/issue', 'GET /api/witness/credentials/{credential_id}', 'GET /api/witness/records/{recordId}/issuer', 'POST /api/witness/credentials/{credential_id}/revoke']
source: >-
  Grounded in openapi/getvda-ai-witness-openapi.json and the "Admission credentials" section of
  https://witness.getvda.ai/llms.txt; the enforcer checklist mirrors the served Onboarding agent card's
  `verification.steps`. MCP equivalents: issue_admission_credential, check_valid, verify_record_issuer,
  revoke_admission_credential.
---

# Issue, check and revoke an admission credential

A credential IS a sealed record. Enforcers hold only the `credential_id` and ask Witness whether it is valid;
revocation is a new superseding record, never a delete.

## Auth
- Issue and revoke need the ISSUING account's key (`Authorization: Bearer wtn....`). `check_valid` and the issuer verdict are PUBLIC — no key.
- For issuer-authenticity "provable even against Witness", issue customer-managed: `POST /api/witness/prepare {skill: "issue_admission_credential", params, signingPublicKeyJwk}` -> sign `canonicalBytes` (raw Ed25519) -> POST the signed record to the returned `submit.endpoint` (structured routing — never parse the prose).

## Steps
1. **Issue** — `POST /api/witness/credentials/issue` with `subject_did`, `issuer_did`, `environment_id`, `scope[]`, `governance_files_hash`, `sandbox_result {pass, score, evidence_seal_ref}`, `impact_delta_ref`, `expires_at`, optional `compliance_mappings[]`. Returns `{credential_id, record, stored: true}`.
2. **Check (enforcer)** — `GET /api/witness/credentials/{credential_id}`. Always HTTP 200; branch on `code` = `valid | revoked | expired | not_found | not_credential`. Read `issuer_verified` / `issuer_verification` separately (`verified | key_not_in_did_doc | custodial | did_unresolvable`) and set your own bar — `key_not_in_did_doc` is loud and suspicious.
3. **Issuer verdict without the body** — `GET /api/witness/records/{recordId}/issuer` returns `signature_valid`, `issuer_verified`, `issuer_did`, `signer_key` and never the record content.
4. **Bind the holder** — a `credential_id` is a bearer handle: valid is necessary, not sufficient. Challenge the presenter to prove control of `subject` via its did:web document (outside Witness). Log every `check_valid` response in your own audit trail.
5. **Revoke (issuer only)** — `POST /api/witness/credentials/{credential_id}/revoke` with `reason_code` (`policy_violation | superseded | compromised | environment_offboarded | other`), `revoked_by`, optional `reason_text`. Terminal; `check_valid` reflects it within ~60s (`Cache-Control: private, max-age=60`).

## Errors
- `403 Not the issuing account` on revoke; `404 Credential not found` (also for a foreign id — no existence leak); `401` on issue without a key.

## Notes
- Publish your record-signing PUBLIC key in your issuer `did:web` document so `issuer_verified` is `true` (Onboarding does this at `onboard.getvda.ai/.well-known/did.json`).
- Witness verifies the assertion is intact, not that the environment exists or the agent controls the DID.
