# ADR-012: Portable recovery bundle format & service-sunset escrow policy

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

FR-5.3 / §5.10: exported bundles must stay recoverable without the production API.

## PRD default position

> Documented, versioned encrypted bundle + annual service-exit exercise.

## Options under consideration

- Container: tar/zip + JSON manifest + binary envelopes
- Manifest integrity: hash tree + owner signature
- What happens to Share A at sunset (depends on ADR-003)

## Proposed direction

Proposal: zip + `manifest.json` (hashes of every entry) + an owner signature. `recovery-cli` validates it offline.

## Consequences / dependencies

- AC-17
- Depends on ADR-001, ADR-003
