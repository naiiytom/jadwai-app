# Security Invariants (Normative)

These rules are **normative**. They come from PRD Appendix B, §5, §7.1 and §13. Each one
must have at least one automated test before the component it governs reaches production.
The *Test* column names the planned test suite, so it can be traced to code later.

| ID | Rule | Source | Test (planned) |
|---|---|---|---|
| INV-01 | MUST NOT: the server or a partner holds enough default material to decrypt a live vault without the owner or a recovery policy. | App. B | `vault-core` key-custody property tests; service API review |
| INV-02 | MUST NOT: Tier 1 unlock exposes any key or share capable of Tier 2 decryption. | FR-4.2, AC-01 | `vault-core::tests::tier1_cannot_decrypt_tier2` (every client path) |
| INV-03 | MUST NOT: an AEAD nonce is reused with the same key. | §5.3, AC-02, NFR-SEC-02 | Property test over generated fixtures; lint for nonce constructors |
| INV-04 | MUST: ciphertext is bound to `vault_id, tier, object_type, schema_version, key_version` through AAD. | §5.3, AC-02 | Ciphertext-swap negative tests |
| INV-05 | MUST NOT: a QR/NFC card stores a raw Shamir share as a permanently valid bearer token. | FR-5.1, §5.5 | Card payload schema test |
| INV-06 | MUST NOT: a verifier approval is reused across unlock requests. Approvals are request-scoped, challenge-bound and expiring. | FR-4.3, AC-03 | Orchestrator model test (replay) |
| INV-07 | MUST NOT: inactivity alone completes a Tier 2 unlock. | FR-4.1, AC-04 | State-machine model test |
| INV-08 | MUST: `RELEASE_DELAY_48H` is entered only after the threshold is satisfied. Owner cancellation invalidates escrow release for that request. | §7.1, FR-4.3 | Orchestrator model test |
| INV-09 | MUST: `SECURITY_FREEZE`/`DISPUTED` blocks release regardless of elapsed delay and never auto-completes. | FR-4.6, AC-15 | Model test with clock advance |
| INV-10 | MUST NOT: third-party tracking or JS loads in the vault origin. | §5.6, AC-07 | CSP/bundle scanner in CI; e2e network allow-list |
| INV-11 | MUST NOT: plaintext, keys or raw financial values enter logs, crash reports or analytics. | §5.6, §10 | Telemetry schema allow-list tests; log-redaction tests |
| INV-12 | MUST NOT: any support/admin path yields plaintext or Tier keys without owner-held material. | FR-1.6, AC-12, AC-13 | Runbook review + API authorization tests |
| INV-13 | MUST: revoked devices can't decrypt material that was re-wrapped after revocation. New-device enrollment emits a security event and a notification. | FR-1.6, AC-14 | Device lifecycle integration test |
| INV-14 | MUST: stale revisions of security-sensitive objects are rejected or surfaced. Cryptographic validity doesn't make an old revision current. | FR-5.5, AC-16 | Offline-reconnect/replay tests |
| INV-15 | MUST: a temporary delegate can't derive `K_TIER2` or satisfy post-death unlock. | FR-4.7, AC-19 | Delegation negative tests |
| INV-16 | MUST: a lead can't be created without a versioned consent receipt. The lead domain has no `user_id`/`vault_id`. | FR-2.3, AC-08 | Schema test + API test |
| INV-17 | MUST NOT: agent, HR or partner roles reach decrypted vault content through any server API. | AC-06 | Authorization matrix tests |
| INV-18 | MUST: verifier, card and share rotation and revocation are supported. Reissue invalidates prior generations. | FR-5.1, AC-05 | Card lifecycle tests |
| INV-19 | MUST: security events are append-only and contain no sensitive plaintext. | NFR-SEC-05 | Store-level tests (no UPDATE/DELETE grants; hash-chain verify) |
| INV-20 | MUST: a portable export is versioned and integrity-checkable, and can be validated offline. | FR-5.3, AC-17 | `recovery-cli verify` against golden bundles |
| INV-21 | MUST: Tier 2 policy can require a verifier IAL independent of the auth provider. No IdP is a cryptographic root for `K_TIER2`. | FR-1.7, AC-18 | Policy engine tests |
| INV-22 | MUST: vault closure revokes cards and verifiers and destroys wrapped/escrow material **before** ciphertext deletion. | FR-5.4, AC-20 | Closure workflow test |
| INV-23 | MUST: legal and benefit information is versioned guidance with a source and an effective date. | §8.5, AC-11 | Config schema validation |

## Change control

Changing, weakening or removing an invariant requires:

1. an ADR with status *Accepted* and sign-off from Security,
2. an update to this file and to `docs/TRACEABILITY.md`,
3. if user-facing claims change (for example "zero-knowledge"), a review of product copy.
