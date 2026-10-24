# ADR-004: Death certificate verification

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

Tier 2 requires death verification. Forgery is a known threat (§5.7).

## PRD default position

> Manual/provider/official verification route. No automatic approval from OCR alone.

## Options under consideration

- Manual review queue with dual control
- Provider/official verification if a route exists
- Evidence stored as an encrypted object with retention

## Decision

Use human review with dual control as the baseline, with official/provider checks where a valid route exists. Retain evidence only as encrypted objects under a counsel-approved retention policy. OCR may assist triage but must never approve. All approvals remain subject to the release delay, owner cancellation, disputes and `SECURITY_FREEZE`; no manual override may bypass those controls.

## Consequences / dependencies

- Counsel/security must define reviewer authority, evidence retention, dispute/appeal and fallback procedures before Phase 2.
