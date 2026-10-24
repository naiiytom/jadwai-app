# ADR-014: Temporary incapacity / delegated-access legal model

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-4.7 / §8.6: delegated access is a product authorization feature, not legal authority.

## PRD default position

> Kept separate from the post-death flow. Limited pilot only after Thai legal/security review.

## Options under consideration

- Tier 1 subset key wrapper for the delegate
- Expiry + revoke + audit

## Decision

Keep delegated access out of the MVP. Consider a Phase 2 pilot only after Thai legal and security review, limited to a Tier 1 subset with explicit expiry, revocation and audit. It remains separate from post-death recovery and grants no legal or medical authority.

## Consequences / dependencies

- AC-19
- No implementation before the required reviews.
