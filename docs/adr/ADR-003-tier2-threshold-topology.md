# ADR-003: Tier 2 threshold topology & escrow implementation

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

PRD §5.4: 2-of-3 with A = platform escrow, B = primary trusted person, C = independent backup. The agent is not a default holder. See **REVIEW-NOTES R-01**: in plain 2-of-3, B+C can bypass every server-side gate.

## PRD default position

> 2-of-3 with platform + 2 owner-selected trusted persons. Agent not default.

## Options under consideration

- (a) Plain 2-of-3 Shamir. Document that B+C collusion is accepted and warn the owner.
- (b) A mandatory: `K = combine(A, any-of(B,C))`. Every server gate holds. Needs an escrow-at-sunset plan for FR-5.3.
- (c) Hybrid: B+C path only after a long delay enforced by an independent time-lock or notary service.
- Share custody for B/C (R-03), sealing to the request's ephemeral key, HSM/KMS for A (R-08), configurable delay ≥ 48 h (R-11).

## Proposed direction

Undecided. This is the most important Phase 0 decision. Prototype (a) and (b) in `vault-core`.

## Consequences / dependencies

- Blocks Phase 2
- Affects INV-07, INV-08, INV-09
