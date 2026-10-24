# ADR-007: Continuity pricing & telecom cost envelope

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-1.1 / §11: the Lifetime Pass can't promise telecom operations forever.

## PRD default position

> Recurring continuity entitlement kept separate from lifetime vault ownership.

## Options under consideration

- Entitlements table where entitlements stack (D-09)
- Escalation channel availability depends on the entitlement

## Decision

Keep recurring Continuity entitlement separate from lifetime vault ownership, and model entitlements as stackable records. Expiry may disable paid notification channels only; it must not remove vault access or interrupt a recovery request already in progress. Price and telecom cost remain product hypotheses.

## Consequences / dependencies

- Continuity expiry behavior is independent of key access and recovery policy.
