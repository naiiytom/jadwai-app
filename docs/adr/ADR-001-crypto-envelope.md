# ADR-001: AEAD cipher suite & canonical envelope format

- **Status:** Accepted
- **Date:** 2026-10-24
- **Source:** PRD v1.1.1 §14 (ADR backlog)
- **Deciders:** Product owner

## Context

PRD §5.3 allows AES-256-GCM or XChaCha20-Poly1305 through a vetted library, with a nonce per object, AAD and key versioning. One canonical format is needed so that `vault-core` (WASM), `recovery-cli` (native) and the export bundle all agree.

## PRD default position

> Either cipher is acceptable via a vetted library. Choose one canonical format with versioning.

## Options under consideration

- **XChaCha20-Poly1305** (RustCrypto `chacha20poly1305`). Random 192-bit nonces are safe across many devices with no coordination, and it's fast in WASM without AES-NI.
- **AES-256-GCM** via WebCrypto. Hardware-accelerated and non-extractable keys are possible, but 96-bit random nonces have a collision budget per key, and WebCrypto ties the browser path to a different implementation than native.
- Envelope encoding: a fixed binary header (versioned) vs CBOR vs Protobuf. It must be canonical for AAD.

## Decision

Use XChaCha20-Poly1305 from a vetted RustCrypto library, a versioned fixed binary header, canonical AAD encoding, and STREAM chunked AEAD for attachments. Generate a fresh random nonce from the CSPRNG for every encryption. AAD binds at least `vault_id`, `tier`, `object_type`, `schema_version` and `key_version`; `object_revision` is included for revision binding. Keep server-visible object types coarse and pad attachment sizes; item counts remain visible for entitlement checks.

## Consequences / dependencies

- FR-5.3 export format depends on this
- AC-02 test design depends on this
- This selects the implementation target, not production readiness. Shared Rust/WASM/offline vectors and external crypto review are required before Phase 1 exit.
