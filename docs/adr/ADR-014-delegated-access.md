# ADR-014: Temporary incapacity / delegated-access legal model

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security, Legal)_

## Context

FR-4.7 / §8.6: delegated access is a product authorization feature, not legal authority.

## PRD default position

> Kept separate from the post-death flow. Limited pilot only after Thai legal/security review.

## Options under consideration

- Tier 1 subset key wrapper for the delegate
- Expiry + revoke + audit

## Proposed direction

Accept the PRD default. Phase 2 pilot only.

## Consequences / dependencies

- AC-19
