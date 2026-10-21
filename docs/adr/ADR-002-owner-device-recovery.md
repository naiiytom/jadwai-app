# ADR-002: Owner account & device recovery construction

- **Status:** Proposed
- **Date:** 2026-10-21
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** _TBD (Architecture, Security)_

## Context

FR-1.6 / AC-13 / AC-14: owner recovery must not create an admin reset path. Devices must be enrollable and revocable, with key re-wrap after recovery.

## PRD default position

> Owner-held recovery material + trusted-device lifecycle + local re-wrap. No support/admin plaintext or key reset.

## Options under consideration

- Device wrapping key: WebAuthn **PRF** extension (passkey-derived) vs non-extractable WebCrypto key in IndexedDB vs both.
- Owner Recovery Key: a printed high-entropy code (e.g., 256-bit, Bech32/word list) that wraps both Tier keys.
- Rotation after a device loss: re-wrap only, vs rotate Tier keys and re-encrypt.

## Proposed direction

Proposal: passkey-PRF where supported, falling back to a non-extractable device key. A mandatory printed Owner Recovery Key with a verification step in onboarding. Re-wrap on revoke, and offer a full rotation if compromise is suspected.

## Consequences / dependencies

- R-09 UX for total loss
