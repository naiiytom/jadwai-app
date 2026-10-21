# ADR-011: Verifier identity proofing provider / assurance mapping

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security, Legal)_

## Context

FR-1.7 / §5.9: authentication is kept separate from identity proofing. Levels IAL-0 to IAL-3.

## PRD default position

> Provider-agnostic IAL policy. Evaluate ThaiD/regulated identity routes without making any provider a Tier 2 cryptographic root.

## Options under consideration

- Provider abstraction interface
- Minimized, retention-limited IdP artifacts

## Proposed direction

Accept the PRD default. Define the IAL→policy mapping and the provider interface.

## Consequences / dependencies

- AC-18
