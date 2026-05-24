# opensubagents-ai subprocessor whitelist + vendor policy

Authoritative list of subprocessors approved to receive data from any opensubagents-ai system or repo. Mirrors the curation discipline of Anthropic's trust page: short, explicit, and gate-kept by ADR.

## Whitelist

The following vendors are pre-approved as subprocessors. Code in any opensubagents-ai repo may route data to these vendors WITHOUT an ADR (but should still document the integration in a relevant README or playbook).

| Vendor | Scope | Notes |
|---|---|---|
| **Cloudflare** | DNS, CDN, Workers, Pages, R2, Analytics GraphQL, Browser Rendering MCP | Primary edge + observability vendor |
| **Turbopuffer** | Vector search | Reserved for future RAG / semantic search workloads |
| **Twilio** | Messaging (SMS, voice), email via SendGrid | Reserved for OMKT1 (promo-credits cold outreach) and future channels work |
| **Brave** | Search API | Reserved for future web-research surfaces |
| **Stripe** | Payments | Reserved for future revenue surfaces |
| **GCP** (Google Cloud Platform) | Compute, GCS, AlloyDB Omni (self-hosted image), Vertex AI | Primary "third hyperscaler" |
| **AWS** (Amazon Web Services) | Compute, S3, RDS, Bedrock | Primary "second hyperscaler" |
| **Azure** (Microsoft Azure) | Compute, Blob Storage, OpenAI Service | Primary "first hyperscaler" |

## ADR-required vendors

Any vendor NOT on the whitelist requires an ADR in `infra/docs/decisions/` before code can land that routes data to that vendor. The ADR must fill the `Subprocessor whitelist impact` block of the template at `infra/docs/decisions/_template.md`.

Currently known non-whitelist vendors that the 7-workstream initiative may require:

| Vendor | Workstream | ADR # | Status |
|---|---|---|---|
| Atlassian (Jira + Confluence via Rovo MCP) | OPM1 (WS1) | 0006 | Proposed |
| Docker, Inc. (Docker Business tier) | ODAT1 (WS5) | 0005 | Proposed |
| MongoDB Atlas | OMKT1 (WS3) | 0009 | Reserved (file only if adopted) |
| Mintlify (docs hosting) | ODOM1 (WS7) | 0010 | Reserved (file only if adopted) |
| Webflow (marketing site) | ODOM1 (WS7) | 0011 | Reserved (file only if adopted) |
| Vercel | OMKT1 (WS3) | TBD | Not yet considered; file if adopted |
| Daytona | OMKT1 (WS3) | TBD | Not yet considered; file if adopted |

## Decision criteria — when to add to the whitelist vs. just file an ADR

A vendor stays in "ADR-required" status (per-decision approval) when ANY of:

- The vendor handles personally identifiable information (PII) about end users
- The vendor handles operator-authentication credentials (OAuth tokens, API keys, session cookies)
- The vendor is a single point of failure for a critical execution path
- The vendor's data residency or compliance posture is unclear

A vendor is promoted TO the whitelist after multiple ADRs successfully clear review for the same vendor, indicating durable adoption \u2014 typically after 3+ accepted ADRs or 6+ months of production use without issue. Whitelist promotion is itself an ADR (`NNNN-whitelist-promotion-<vendor>.md`).

## Decision criteria \u2014 when to REMOVE from the whitelist

A vendor is removed if:

- The vendor's terms change in a way that breaks the org's compliance posture
- The vendor announces sunset / acquisition that disrupts continuity
- An incident (data loss, security breach, prolonged outage) makes them untrustworthy

Removal is also an ADR (`NNNN-whitelist-removal-<vendor>.md`) and supersedes whichever ADR added the vendor.

## Compliance posture

- **DPA (Data Processing Agreement):** Required for any whitelist vendor handling PII. Operator maintains the executed DPA list in `infra/docs/compliance/dpa-inventory.md` (not yet created; first DPA triggers the file).
- **SOC2 / ISO27001:** Preferred for production data stores. The operator's preference: pick vendors with public SOC2 Type II reports.
- **BAA (Business Associate Agreement) under HIPAA:** Not applicable until opensubagents-ai serves a healthcare customer.
- **Subprocessor disclosure:** Mirror Anthropic's pattern \u2014 if opensubagents-ai ever serves end customers, this file becomes the public subprocessor disclosure.

## Process

1. Author proposes the vendor (typically in a workstream PRD or a PR description).
2. Author opens an ADR in `infra/docs/decisions/NNNN-<vendor>.md` using `_template.md`.
3. ADR fills the Subprocessor block honestly: what data, what frequency, what retention.
4. Reviewer (operator + at least one engineering IC) accepts or rejects.
5. On accept: ADR status flips, vendor goes into the "ADR-required (cleared)" section of this file.
6. On reject: ADR stays as a record; the alternative is pursued.

## See also

- [Anthropic's subprocessor list](https://www.anthropic.com/legal/trust-and-compliance) \u2014 the mirroring discipline reference
- `infra/docs/decisions/` \u2014 ADRs live here
- `infra/docs/decisions/_template.md` \u2014 the template
- `CONVENTIONS.md` \u2014 commit + branch conventions
