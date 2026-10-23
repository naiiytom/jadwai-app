# Data Model (logical)

PRD §6 is a **logical baseline, not migration-ready DDL**. This page groups the tables
by domain, records the separation rules, and lists the changes proposed during repo setup.
Real migrations go in `db/migrations/` only after ADR-013 (revisions), ADR-003 (recovery
topology) and ADR-016 (stack) are accepted.

## 1. Tables by domain

| Domain | Tables (PRD §6) | Notes |
|---|---|---|
| Identity | `users`, `trusted_devices` | `users.email` is CITEXT. Phone is stored as a hash only |
| Vault | `vaults`, `encrypted_vault_objects`, `key_wrappers`, `document_provenance`, `portability_exports` | Ciphertext + wrapped keys only |
| Recovery | `vault_verifiers`, `physical_cards`, `unlock_requests`, `unlock_approvals`, `unlock_disputes` | Escrow Share A is wrapped by KMS/HSM |
| Lead | `consent_receipts`, `anonymous_leads` → **rename `privacy_limited_leads`** | **No** `user_id`/`vault_id` |
| Security | `security_events` | Append-only/WORM |

## 2. Separation rules (PRD §6.1)

- Vault ciphertext, leads, partner CRM and marketing analytics are **separate logical domains with separate DB roles**. Proposal: separate databases, or at least separate schemas with no cross-schema grants.
- Lead tables have no direct `user_id`/`vault_id`. Correlation, if needed at all, uses a one-time token or consent-receipt linkage with retention limits.
- Verifier contacts are encrypted at rest. A deterministic hash is kept only where lookup or dedup needs it, and it is a keyed HMAC, never a bare hash of a phone number.
- Death certificates and evidence are encrypted objects. No public or predictable URLs.
- Security events are append-only and never hold plaintext sensitive payloads.

## 3. Proposed changes to the PRD baseline

Each one needs confirmation in the ADR listed.

| # | Table | Change | Why | ADR |
|---|---|---|---|---|
| D-01 | `encrypted_vault_objects` | Add `logical_object_id UUID NOT NULL`, `object_revision BIGINT NOT NULL`, `parent_revision BIGINT NULL`, `writer_device_id UUID NULL`. Make `UNIQUE(logical_object_id, object_revision)` | §5.3 says "key rotation without changing business identity" and FR-5.5 asks for rollback protection. The row `id` alone can't express either | ADR-013 |
| D-02 | `encrypted_vault_objects` | Review whether a plaintext `object_type` leaks too much (e.g., `LIVING_WILL`, `FUNERAL_PREF`). Consider coarse server-visible types plus a fine type inside the ciphertext | Metadata minimization, PDPA §26 sensitivity | ADR-001 |
| D-03 | `vault_verifiers` | Add `identity_assurance_level`, `ial_verified_at`, `ial_provider_ref`, `last_revalidated_at` | FR-1.7, AC-18, NFR-DATA-01 | ADR-011 |
| D-04 | `unlock_requests` | Add `card_id`, `card_generation`, `requester_ref_hash`, `request_public_key`, `challenge_nonce`, `expires_at`, `correlation_group_id`, `policy_version` | Request-scoped binding, competing-request correlation, card revocation policy | ADR-003/005 |
| D-05 | `unlock_approvals` | Add `sealed_share BYTEA NULL`. Represent the platform escrow component as a separate `escrow_releases` record, not as a verifier | The platform is Share A, not a `vault_verifiers` row | ADR-003 |
| D-06 | `security_events` | Add `seq BIGINT`, `prev_hash BYTEA`, `event_hash BYTEA`, and revoke UPDATE/DELETE from all app roles | Tamper evidence for "append-only" | — |
| D-07 | `anonymous_leads` | Rename to `privacy_limited_leads`. Add `consent_receipt_id NOT NULL` (FK inside the lead domain only), `retention_until` | FR-2.3 rename, AC-08 | ADR-010 |
| D-08 | `consent_receipts` | Clarify `subject_ref` semantics. In the lead domain it must **not** equal `users.id` | Lead isolation | ADR-010 |
| D-09 | `users` | Move `vault_status`, `inactivity_days` and `last_heartbeat_at` to a `vault_heartbeats` table in the recovery domain. Move `tier` to an `entitlements` table (Free / Lifetime / Sponsored can stack) | Identity shouldn't own recovery state. Entitlements combine | ADR-007 |
| D-10 | `users.sponsored_by_agent_id` | Move to a partner-domain `sponsorships` table with start and end dates | FR-3.1: terminating a sponsorship must not touch the vault | — |
| D-11 | new | `delegations` (scope, expiry, status), `entitlements`, `escrow_releases`, `benefit_config_versions`, `legal_template_versions` | FR-4.7, FR-1.1, AC-11, FR-1.3 | various |
| D-12 | `key_wrappers` | Add `wrapper_generation`, `device_id NULL`, and a `wrapper_type` value for `DELEGATE_TIER1_SUBSET` | Device revocation and re-wrap (AC-14), delegation | ADR-002 |

## 4. PRD baseline (verbatim reference)

See [PRD §6](../prd/PRD-v1.1.1.md#6-core-data-model-postgresql---revision-111).
