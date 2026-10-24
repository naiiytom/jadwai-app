# ADR-007: Pricing & notification cost envelope

- **Status:** Accepted for notification-expiry boundary; pricing remains a hypothesis
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog), §11
- **Deciders:** Product owner

## Context

FR-1.1 / §11: a one-time Lifetime Pass can't fund recurring per-message costs forever.
PRD v1.1.1 handled that with a separate recurring Continuity Service (299 THB/year).

## Accepted decision: notification entitlement boundary

Expiry may disable paid notification channels only. It must not remove vault access or
interrupt a recovery request already in progress. The Lifetime Pass price, notification cost
envelope and service-exit funding remain hypotheses and open decisions below; this ADR does
not approve a price or authorize sales.

## Historical PRD baseline (v1.1.1)

> Recurring continuity entitlement kept separate from lifetime vault ownership.
> Lifetime Vault Pass 990 THB one-time, Continuity Service 299 THB/year (both hypotheses).

## Options under consideration

- Keep the separate Continuity Service (PRD baseline).
- Single Lifetime Pass, heartbeat on LINE/Email/push only.
- Single Lifetime Pass with capped bundled SMS credits.

## Provisional direction (owner-proposed 2026-10-22, **not accepted**)

A single Lifetime Vault Pass at 399 THB (hypothesis) and no Continuity Service. Heartbeat
escalation (FR-4.1) stays in Phase 2 over LINE, Email and push. SMS and IVR would be removed from
the state machine. If accepted, this replaces the baseline above. Until then the PRD edits that
follow it are design work, not a sales or implementation approval.

- INV-07 is unchanged: inactivity still never releases Tier 2.
- `entitlements` (D-09) would hold Free / Lifetime / Sponsored, with no recurring consumer entitlement.
- Removing SMS/IVR removes per-message telecom cost. It does **not** make delivery free: LINE
  Messaging API has free allowances, paid plans and extra-message charges, and exceeding the
  allowance can stop delivery (<https://developers.line.biz/en/docs/messaging-api/pricing/>).
  Email and push carry provider costs and quotas too.

## Cost envelope (assumption, to be sized)

- 399 THB is assumed VAT-inclusive: 399 / 1.07 is about 373 THB net, before payment fees.
- That has to cover, for the life of each vault: attachment storage, KMS/HSM escrow keys (R-08),
  messaging provider usage, initial card fulfilment, and the service-exit exercise (NFR-DUR-01).
- Phase 3 lead revenue is unresolved (R-13, R-16) and MUST NOT be assumed to fund continuity.

## Open decisions (each needs an owner and gate before the pass is sold)

| ID | Decision | Owner | Gate |
|---|---|---|---|
| OD-1 | Messaging cost envelope per vault and quota-exhaustion fallback (for example Email and push continue if LINE is exhausted; the owner sees a degraded-delivery state) | Architecture, Product | ADR acceptance |
| OD-2 | Lifetime scope: when hosted service may end, funded reserve or sunset policy, notice and export window | Product, Legal | Before any sale; FR-5.3 service-exit plan |
| OD-3 | Attachment fair-use limit and initial card fulfilment cost | Product | Before pricing is published |
| OD-4 | Effect of failed owner alerts on the 48 h release process. Proposed: fail closed, never shorten or skip the delay (INV-07, INV-08, INV-09) | Security, Product | Phase 2 state-machine model tests (ADR-003) |
| OD-5 | Reminder timing, channel selection, failure and fallback behaviour | Product, Engineering | Phase 2 design freeze |
| OD-6 | Entitlement matrix for heartbeat and channels (Free / Lifetime / Sponsored, sponsorship expiry). Provisional version in PRD FR-4.1 | Product | Phase 1 (D-09) |
| OD-7 | Inventory of the actual LINE, email and push providers for the RoPA and processor register | Security, DPO | R-10 / ADR-010 |
| OD-8 | Copy review of lifetime/sunset and delivery disclosures. `config/` legal and benefit content is still placeholder with counsel review pending (INV-23, AC-11) | Counsel | Before production |

## Consequences / dependencies

- Onboarding copy must disclose that alerts can be delayed, missed or dropped, and that silence
  is neither death evidence nor approval (PRD §4.1 step 8).
- The verifier readiness ping and recovery kit carry more weight, because delivery is best-effort.
- Phase availability: heartbeat and the physical card are Phase 2. Phase 1 ships neither.
- PRD §11, FR-1.1, FR-4.1, FR-5.3, STATE-MACHINES §1, CONTEXT, README, ROADMAP and
  TRACEABILITY carry the provisional wording and point back here.
