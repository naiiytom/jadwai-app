# ADR-004: Death certificate verification

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security, Legal)_

## Context

Tier 2 requires death verification. Forgery is a known threat (§5.7).

## PRD default position

> Manual/provider/official verification route. No automatic approval from OCR alone.

## Options under consideration

- Manual review queue with dual control
- Provider/official verification if a route exists
- Evidence stored as an encrypted object with retention

## Proposed direction

Manual review + release delay + owner cancellation as the baseline. OCR may assist, never approve.

## Consequences / dependencies

- _To be completed when the ADR is accepted._
