# ADR-013: Multi-device concurrency / revision protocol

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

FR-5.5 / §5.8: stale, replayed and rolled-back security-sensitive writes must be detected.

## PRD default position

> Authenticated monotonic revision lineage. Reject or surface stale security-sensitive writes.

## Options under consideration

- Server CAS on (logical_object_id, parent_revision)
- Revision inside the AAD
- Owner-signed vault head against rollback by omission (R-06)

## Proposed direction

Proposal: all three. Schema change D-01.

## Consequences / dependencies

- AC-16
