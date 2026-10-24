# ADR-010: Sensitive data lawful bases & explicit consent UX

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security, Legal)_

## Context

§8.1: PDPA §26 sensitive data (health, religion). Purposes are separate for vault, sensitive data, marketing/leads and partner contact.

## PRD default position

> Finalize with DPO/legal counsel and RoPA.

## Options under consideration

- Purpose codes catalog
- Consent text versioning
- Processor register (telecom, IdP, payment)

## Proposed direction

The technical privacy boundaries are agreed: the lead domain is not zero-knowledge and must say so at consent; it uses its own KMS key and decrypts contact data only after assignment to the accepting partner. Readiness sharing is coarse, opt-in and revocable. Beneficiary status remains in the vault unless the employee explicitly shares a specific status with HR. Partner references use relationship-scoped pseudonyms resolved by Identity Service.

Lawful bases, purpose catalog, consent wording, processor register and retention periods remain pending DPO/counsel. Do not ship lead or partner processing until those are approved.

## Consequences / dependencies

- R-10
- D-07, D-08
- R-20, R-21, R-22, R-23
