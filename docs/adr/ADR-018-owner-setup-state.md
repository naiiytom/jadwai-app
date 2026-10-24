# ADR-018: First-time owner setup state

- **Status:** Proposed
- **Date:** 2026-10-23
- **Deciders:** _TBD (Product, Architecture, Security)_
- **Related:** PRD FR-1.6, Phase 0, Phase 1; ADR-001, ADR-002, ADR-005, ADR-013, ADR-016

## Context

Phase 1 includes account/passkey setup, separate Tier 1 and Tier 2 encrypted objects, and
trusted-device enrollment/revocation. PRD §4.1 defines the first-time owner journey but not
when setup is complete or how an interrupted setup resumes. Contributing requires an ADR
for state-machine transitions. The recovery, authentication, crypto, and revision decisions
that determine the underlying completion checks remain proposed.

## Options

1. Track only whether onboarding started or completed.
2. Track the last completed setup milestone, without defining its cryptographic or identity
   mechanisms here.

## Decision

Proposed: use the second option with the milestones `NOT_STARTED`, `ACCOUNT_READY`,
`VAULT_CREATED`, `TIERS_CONFIGURED`, `RECOVERY_READY`, `DEVICE_READY`, and `VAULT_READY`,
in that order, as shown in [`STATE-MACHINES.md`](../architecture/STATE-MACHINES.md). The
progress state is resumable and MUST NOT act as an authentication, authorization, recovery,
or key-release signal. `VAULT_READY` means initial vault setup is complete; it does not
require optional vault content to be populated.

This ADR does not decide credential factors, recovery material or verification, key
hierarchy, device-enrollment protocol or notices, persistence, or concurrent-write handling.
Those checks must follow the accepted ADRs listed above. No Phase 1 implementation should
begin until the Phase 0 design-freeze gates in PRD §12 are complete, including the owner-
recovery threat model and tested owner-held recovery path. Legal or benefit copy that may
appear in onboarding remains subject to the applicable Thai counsel review; this ADR
defines no such copy.

## Consequences

- The UI can resume from the last completed setup milestone without treating partial work
  as a usable vault.
- ADR-002, ADR-005, and ADR-001 must supply the actual completion criteria before the
  associated transitions are implemented; ADR-013 and ADR-016 govern persistence and
  runtime integration where applicable.
- Acceptance of this progress model does not accept or resolve those dependent ADRs or
  satisfy Phase 0 gates.

## Security review

- [ ] Confirm setup progress cannot authorize access, recovery, or key release.
- [ ] Review completion guards against the accepted crypto, authentication, and recovery
  decisions before implementation.
