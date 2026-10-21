# CLAUDE.md

Guidance for Claude Code and other AI agents working in this repository.

## Project in one paragraph

`jadwai-app` implements the **Digital Legacy & Coverage Gap Vault** for Thailand. It is a
client-side encrypted vault (Tier 1 Emergency Capsule + Tier 2 Estate Vault) with
owner-defined recovery for heirs. Coverage gaps are computed on the device. Revenue comes
from consented leads and B2B2C (insurance agents, Corporate HR), and partners never get
plaintext access. The repo is in **Phase 0 (design freeze)**: docs and skeleton only.

## Source-of-truth order

1. `docs/prd/PRD-v1.1.1.md`: requirements (converted from the `.docx` in the same folder). Cite IDs such as `FR-4.3`, `NFR-SEC-01` and `AC-15`.
2. `docs/security/INVARIANTS.md`: normative MUST / MUST NOT rules. **These override convenience, deadlines and other docs.**
3. `docs/adr/`: accepted ADRs override the PRD "default position". *Proposed* ADRs are not binding yet.
4. `docs/ARCHITECTURE.md`, `docs/architecture/*`: derived design. Update them when an ADR changes.

If code and docs disagree, stop and flag it. Don't silently pick one.

## Hard rules (never violate; the PR will be rejected)

- **Tier separation:** nothing reachable from a Tier 1 recovery path may hold, derive or unwrap `K_TIER2`. UI hiding is not access control. (AC-01)
- **No custom crypto.** Use vetted libraries only (RustCrypto AEADs, a reviewed Shamir/threshold crate, WebAuthn). Never hand-roll primitives, nonce schemes or KDFs.
- **Nonces:** every encryption gets a fresh random nonce from the CSPRNG. Never use a vault-wide IV or a counter shared across devices. (AC-02)
- **AAD** must bind at least `vault_id, tier, object_type, schema_version, key_version`. Decryption must fail if any field differs.
- **Approvals** are rows keyed by `unlock_request_id` with a challenge and an expiry. Never add a persistent `has_approved_unlock`-style flag. (AC-03)
- **Inactivity** states (`HEARTBEAT_MISSED`, `OWNER_ESCALATION`, `VERIFIER_CONTACTED`) never release Tier 2 material. (AC-04)
- **`SECURITY_FREEZE`** never auto-resolves on a timer. (AC-15)
- **No support/admin bypass**: no endpoint, script or migration may reset an account in a way that yields plaintext or Tier keys. (AC-12, AC-13)
- **Vault origin hygiene:** no third-party JS, CDN scripts, analytics, ads, pixels, session replay or remote error reporters that capture DOM or state in `apps/web-vault`. CSP violations fail the build. (AC-07)
- **Logging/telemetry:** never log plaintext, keys, shares, nonces paired with keys, raw balances, policy numbers or verifier contacts. Analytics events are allow-listed and schema-validated, and unknown fields are rejected. (NFR-PRIV-01, §10)
- **Lead domain:** lead tables never carry `user_id`/`vault_id`, and lead creation requires a versioned consent receipt. (AC-08)
- **Partners/HR/agents** get no server API that returns decrypted vault content. Agents are never a default share holder. (AC-06)
- **Stale writes:** security-sensitive objects (will, verifier, recovery policy, beneficiary status) carry `object_revision`/`parent_revision`, and stale writes are rejected or surfaced as conflicts, never silently overwritten. (AC-16)

## Legal/regulatory copy rules

- §1657 output is always labelled a **guide**, never a will. Never add a "print and sign" CTA for 1657. (AC-09)
- §1657 guide states that the whole text, the date and the signature must be handwritten, that a fingerprint or mark **can't** replace the signature, and that alterations must be handwritten and signed.
- §1656 signing guide states that **at least two witnesses are present together**. (AC-10) The checklist also warns that witnesses (and the writer) **and their spouses can't inherit under that will** (CCC §1653), and lists who can't be a witness (§1670).
- The will flow requires the testator to be ≥ 15 (CCC §25).
- Contextual cards and quote flows must not contain links that facilitate contract formation, give personalized suitability advice, or imply performance-based pay, pending counsel review of the OIC B.E. 2569 guidelines (REVIEW-NOTES R-16).
- Living Will (NHA §12) ≠ generic DNR. Self-reported medical info is visually distinct from legal documents.
- Benefit amounts and legal references live in `config/` with `source_url`, `effective_date` and `version`. Never hard-code amounts in UI copy (e.g., never write "everyone gets 50,000 THB"). (AC-11)
- Never call the lead flow "anonymous". Its name is **Privacy-Limited Quote**.
- Never claim the platform changes a beneficiary with a provider. Use the status hub states.
- Never present the vault as an OIC/national policy database.
- Thai-first copy. Legal and benefit copy is versioned per locale.

## Repo layout (planned)

```text
apps/       web-marketing (www) · web-app (app shell) · web-vault (vault origin) · web-partners · web-lead
crates/     vault-core (envelope, keys, AAD, revisions) · vault-wasm · risk-engine · recovery-cli
services/   identity · vault-api · recovery-orchestrator · notification · lead-isolation · partner-registry · security-events
db/         migrations (none yet: PRD §6 schema is logical, see docs/architecture/DATA-MODEL.md)
config/     benefits/ · legal-templates/ (versioned, sourced)
```

Each origin is a **separate build and deployment**. Do not share cookies, service workers or bundles between `vault` and the other origins.

## Commands

Not defined yet. When the first toolchain lands (ADR-016), record the build, test, lint and fmt commands here.

## Working conventions

- Use RFC 2119 wording (MUST, SHOULD) in specs. Tag requirements with PRD IDs in docs, tests and PR descriptions.
- Any change to crypto format, key hierarchy, recovery topology, state machine or data-domain boundaries **needs an ADR** (`docs/adr/0000-template.md`) before code.
- Every new FR, NFR or AC must be added to `docs/TRACEABILITY.md`.
- Security invariants need **automated tests in CI** (property tests for nonce uniqueness, Tier 1→Tier 2 negative tests, state-machine model tests, CSP tests).
- Pin dependencies, commit lockfiles and review lockfile diffs. Adding a dependency to `vault-core` or `apps/web-vault` needs justification in the PR.
- Keep test fixtures synthetic. Never use real Thai ID numbers, policy numbers or personal data.
- Open questions go to `docs/architecture/REVIEW-NOTES.md`. Don't invent answers to legal questions.
