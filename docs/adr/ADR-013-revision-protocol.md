# ADR-013: Multi-device concurrency / revision protocol

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-5.5 / §5.8: stale, replayed and rolled-back security-sensitive writes must be detected.

## PRD default position

> Authenticated monotonic revision lineage. Reject or surface stale security-sensitive writes.

## Options under consideration

- Server CAS on (logical_object_id, parent_revision)
- Revision inside the AAD
- Owner-signed vault head against rollback by omission (R-06)

## Decision

Use server compare-and-swap on `(logical_object_id, parent_revision)`, bind `object_revision` in AAD, and retain a last-seen revision per device. Reject stale writes and surface conflicts for security-sensitive objects. Do not add the owner-signed vault head in this version.

A device with no previously trusted checkpoint may not detect that a server returned an older, internally consistent snapshot. Document this limitation; any stronger first-device rollback defense requires a separate security decision.

## Consequences / dependencies

- AC-16
