# ADR-005: Tier 1 card & authentication policy

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

FR-4.1 Path B / FR-4.2: card + designated verifier/auth reveals K_TIER1 only. REVIEW-NOTES R-02: the construction isn't specified.

## PRD default position

> Card + designated verifier/auth. Must not disclose Tier 2 material.

## Options under consideration

- Card secret + server activation secret per generation → HKDF → unwrap K_TIER1
- Card-only (rejected: a stolen card would be enough)
- Verifier-only (no physical factor)

## Decision

The card package is `AEAD(K_card, K_TIER1)`, where `K_card = HKDF(card_secret || activation_secret_gen)`. Require card secret, verifier authorization, and a current revocation check. Revocation invalidates the generation's activation material. Tier 1 must never expose or derive Tier 2 material (AC-01).

At service sunset, the offline recovery bundle must preserve verifier authorization using verifier-held offline material; the owner-held bundle plus card alone must not unlock Tier 1. The exact offline verifier artifact and revocation proof remain an implementation gate.

## Consequences / dependencies

- AC-01, AC-05
