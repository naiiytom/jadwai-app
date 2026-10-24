# Architecture Decision Records

ADR-001 to ADR-015 come from the PRD v1.1.1 §14 backlog. ADR-016 and ADR-017 were added
when the repo was set up; ADR-018 records the proposed first-time owner setup state. New
ADRs copy [`0000-template.md`](0000-template.md) and take the next number.
Accepted decisions record the agreed design; they do not waive the implementation and review gates listed in each ADR and the roadmap.

| ADR | Title | Status | Phase gate |
|---|---|---|---|
| [001](ADR-001-crypto-envelope.md) | AEAD cipher suite & canonical envelope | Accepted | 0 |
| [002](ADR-002-owner-device-recovery.md) | Owner account & device recovery | Accepted | 0 |
| [003](ADR-003-tier2-threshold-topology.md) | Tier 2 safeguards & escrow (R-25 implementation gate) | Accepted; implementation gated | 0 |
| [004](ADR-004-death-verification.md) | Death certificate verification | Accepted; operational/legal details gated | 2 |
| [005](ADR-005-tier1-authentication.md) | Tier 1 card & authentication policy | Accepted; offline protocol gated | 0 |
| [006](ADR-006-web-vs-native.md) | Web vs native companion | Accepted | 0 |
| [007](ADR-007-pricing-notification-cost.md) | Pricing & notification cost | Accepted expiry boundary; pricing remains a hypothesis | 1 |
| [008](ADR-008-oic-integration.md) | OIC integration | Accepted | 2 |
| [009](ADR-009-partner-licensing.md) | Partner licensing verification | Proposed | 3 |
| [010](ADR-010-pdpa-lawful-basis.md) | PDPA lawful bases & consent UX | Proposed; privacy boundaries agreed | 0 |
| [011](ADR-011-verifier-identity-assurance.md) | Verifier identity assurance | Proposed; mapping is a Phase 0 gate | 0 |
| [012](ADR-012-portable-recovery-bundle.md) | Portable recovery bundle & sunset | Accepted; R-25 implementation gate | 0 |
| [013](ADR-013-revision-protocol.md) | Multi-device revision protocol | Accepted; fresh-device limit documented | 0 |
| [014](ADR-014-delegated-access.md) | Delegated access legal model | Accepted; feature deferred | 2 |
| [015](ADR-015-document-provenance.md) | Document provenance sources | Accepted | 1 |
| [016](ADR-016-implementation-stack.md) | Implementation stack | Accepted; catalog-key rotation gated | 0 |
| [017](ADR-017-monorepo-layout.md) | Monorepo layout | Accepted | 0 |
| [018](ADR-018-owner-setup-state.md) | First-time owner setup state | Proposed; Phase 0 gated | 1 |
