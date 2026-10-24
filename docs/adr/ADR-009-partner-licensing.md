# ADR-009: Partner licensing verification source & cadence

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security, Legal)_

## Context

§8.2: partner license status needs verification and `last_verified_at`. The UI warns on expired status.

## PRD default position

> Use official verification where available. Record `last_verified_at`.

## Options under consideration

- Manual verification with periodic re-check
- Official lookup if available

## Proposed direction

Use official license verification where an authoritative route exists; otherwise use documented manual verification. The re-verification cadence, expiry threshold, monetized lead model, and contextual card wording remain subject to counsel/OIC confirmation. Until then, contextual cards are educational only, with no contract-forming links or personalized suitability advice, and no performance-based compensation tied to policies or premiums.

## Consequences / dependencies

- This ADR remains Proposed until legal review resolves the licensing and monetization questions.
