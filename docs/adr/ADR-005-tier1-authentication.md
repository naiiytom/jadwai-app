# ADR-005: Tier 1 card & authentication policy

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

FR-4.1 Path B / FR-4.2: card + designated verifier/auth reveals K_TIER1 only. REVIEW-NOTES R-02: the construction isn't specified.

## PRD default position

> Card + designated verifier/auth. Must not disclose Tier 2 material.

## Options under consideration

- Card secret + server activation secret per generation → HKDF → unwrap K_TIER1
- Card-only (rejected: a stolen card would be enough)
- Verifier-only (no physical factor)

## Proposed direction

Proposal: card secret + per-generation server activation secret released after verifier auth and a revocation check. Revocation deletes the activation secret.

## Consequences / dependencies

- AC-01, AC-05
