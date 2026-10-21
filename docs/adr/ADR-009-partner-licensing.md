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

Accept the PRD default. Define a re-verification cadence and an expiry warning threshold.

## Consequences / dependencies

- _To be completed when the ADR is accepted._
