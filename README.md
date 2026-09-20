# VDA / GOSCE

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Verified Digital Agents (VDA, getvda.ai) sells governance for AI agents that can be proved after the fact — a composable suite of governance blocks: **Witness** seals every governed decision into an Ed25519-signed, hash-chained record (anchored to Sigstore Rekor and RFC 3161 timestamp authorities on the paid tier) and produces EU AI Act Article 12 evidence; **C2MD** turns EU AI Act, GDPR, NIST SP 800-53 and ISO 42001 controls into governance Markdown agents can follow; **ACP** versions, tests, approves and signs that governance in git; **Onboarding** admits agents and issues Witness-sealed admission credentials against did:web identities; **HITL** routes what exceeds an agent's authority to a human and seals the decision. The same operator runs **GOSCE**, a 98-server fleet of templated, x402-metered MCP/A2A agents under `*.getvda.ai` (the "VDA / GOSCE" author on a2aregistry.org).

What this profile holds (all fetched from the provider's own hosts on 2026-09-19):

- `openapi/` — six verbatim OpenAPI 3.1 documents (Witness 23 ops, HITL 17, ACP 12, C2MD edge 9, GOSCE portfolio 14, GOSCE router 14)
- `a2a/` — eight served A2A agent cards, graded against A2A 1.0.0 (five conformant, three flavored)
- `mcp/` — four probed remote MCP servers (Witness 16 tools, C2MD 11, HITL gated, GOSCE router 2) with verbatim `tools/list` results and a tool-to-REST crosswalk
- `well-known/` — did:web documents on five hosts, the fleet JWKS and its 99-entry agent catalog; no security.txt / api-catalog / OAuth metadata anywhere
- `packages/` — ten first-party npm/PyPI packages with live versions and dates
- `llms/` — five provider-published llms.txt files
- plus authentication, scopes, conformance, errors, lifecycle, conventions (idempotency + reversibility), plans, rate limits, sandbox, regulatory posture, data model, an OpenAPI overlay and four agent skills

- Website: https://getvda.ai/
- Witness docs: https://witness.getvda.ai/docs
- GOSCE agent index: https://agents.getvda.ai/agents
- GitHub: https://github.com/getvda-ai
