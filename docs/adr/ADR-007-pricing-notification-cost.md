# ADR-007: Pricing & notification cost envelope

- **Status:** Proposed
- **Date:** 2026-10-22
- **Source:** PRD v1.1.1 §14 (ADR backlog), §11
- **Deciders:** _TBD (Architecture, Security)_

## Context

FR-1.1 / §11: a one-time Lifetime Pass can't fund recurring per-message costs (SMS, IVR)
forever. PRD v1.1.1 resolved that with a separate recurring Continuity Service (299 THB/year).

## PRD default position

> Recurring continuity entitlement kept separate from lifetime vault ownership.

## Options under consideration

- Keep the separate Continuity Service (PRD default).
- Single Lifetime Pass, heartbeat on free channels only.
- Single Lifetime Pass with capped bundled SMS credits.

## Proposed direction

Product decision (2026-10-22): **single Lifetime Vault Pass at 399 THB (hypothesis), no
Continuity Service.** Heartbeat escalation (FR-4.1) stays in Phase 2 on free channels only
(LINE, Email, push). SMS and IVR are removed from the state machine.

- Supersedes the PRD default and the 990 THB / 299 THB-per-year hypotheses in §11.
- INV-07 is unchanged: inactivity still never releases Tier 2.
- `entitlements` (D-09) keeps Free / Lifetime / Sponsored. There is no recurring consumer entitlement.

## Consequences / dependencies

- Net per pass is about 373 THB after 7% VAT, before payment fees. That has to cover
  attachment storage, KMS/HSM escrow keys (R-08) and the service-exit exercise (NFR-DUR-01)
  for the life of the vault. The "lifetime" promise must read "vault access and export",
  not "operations forever".
- Revenue beyond the pass rests on Agent SaaS, HR and leads (Phase 3, R-13/R-16).
- Free-channel delivery is best-effort. Onboarding copy must say so. The verifier readiness
  ping and the recovery kit carry more weight as a result.
- Service-exit plan (PRD §7, NFR-DUR-01) needs a funded reserve or stated sunset policy.
- PRD §11 and FR-1.1, STATE-MACHINES §1, CONTEXT, README and ROADMAP are updated to match.
- Re-check the processor register (R-10, ADR-010): no telecom processors.
