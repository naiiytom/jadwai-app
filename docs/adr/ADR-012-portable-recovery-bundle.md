# ADR-012: Portable recovery bundle format & service-sunset escrow policy

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-5.3 / §5.10: exported bundles must stay recoverable without the production API.

## PRD default position

> Documented, versioned encrypted bundle + annual service-exit exercise.

## Options under consideration

- Container: tar/zip + JSON manifest + binary envelopes
- Manifest integrity: hash tree + owner signature
- What happens to Share A at sunset (depends on ADR-003)

## Decision

Use a versioned ZIP bundle with `manifest.json`, a hash for every entry, and an owner signature. The bundle contains encrypted objects and attachments, crypto/version metadata, recovery metadata, card/verifier generations, required offline authorization material, and human-readable instructions. The open-source, signed-release `recovery-cli` validates and recovers bundles with no network access; the bundle identifies its compatible format/tool version without duplicating the source tree.

Service-sunset export must preserve the Tier 1 verifier factor and all Tier 2 safeguards. A usable Share A must not be placed in an owner-controlled bundle. Any escrow export requires dual control. Exact portable authorization, cancellation/freeze proof and successor escrow mechanics remain an implementation gate (R-25).

## Consequences / dependencies

- AC-17
- Depends on ADR-001, ADR-003
- Run an end-to-end service-exit exercise at least annually (NFR-DUR-01).
