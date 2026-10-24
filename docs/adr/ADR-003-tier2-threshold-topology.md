# ADR-003: Tier 2 threshold topology & escrow implementation

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

PRD §5.4: 2-of-3 with A = platform escrow, B = primary trusted person, C = independent backup. The agent is not a default holder. See **REVIEW-NOTES R-01**: in plain 2-of-3, B+C can bypass every server-side gate.

## PRD default position

> 2-of-3 with platform + 2 owner-selected trusted persons. Agent not default.

## Options considered

- (a) Plain 2-of-3 Shamir. Document that B+C collusion is accepted and warn the owner.
- (b) A mandatory: `K = combine(A, any-of(B,C))`. Every server gate holds. Needs an escrow-at-sunset plan for FR-5.3.
- (c) Hybrid: B+C path only after a long delay enforced by an independent time-lock or notary service.
- Share custody for B/C (R-03), sealing to the request's ephemeral key, HSM/KMS for A (R-08), configurable delay ≥ 48 h (R-11).

## Decision

Use the hybrid topology: Share A is released only through the recovery state machine; B+C recovery is permitted only after an owner-configurable delay of at least 48 hours, enforced by an independent, portable, offline-verifiable authorization protocol. The same death-verification, owner-cancellation, dispute and `SECURITY_FREEZE` safeguards must apply to every path. A timer that merely delays B+C reconstruction does not satisfy this decision.

Seal each verifier share to a verifier-held key (passkey PRF when supported, with an offline QR/printed backup treated as a bearer secret). On approval, reseal the share to the request's ephemeral public key. Wrap Share A with KMS/HSM and release it only under the Recovery Orchestrator's state-machine identity. Any manual action or service-sunset export requires dual control and must not bypass a cancellation or freeze. Never place a usable Share A in an owner-controlled bundle.

**Implementation gate:** no production recovery path may ship until the independent protocol can preserve all listed safeguards after service sunset. The provider, proof format, cancellation/freeze publication and successor escrow procedure remain open Phase 0 security work (R-25).

## Consequences / dependencies

- Blocks Phase 2
- Affects INV-07, INV-08, INV-09
- The accepted policy is not an implementation specification; resolve R-25 before Phase 0 exit.
