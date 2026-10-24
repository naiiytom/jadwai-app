# ADR-016: Implementation stack

- **Status:** Accepted
- **Date:** 2026-10-24
- **Deciders:** Product owner

## Context

The PRD names PostgreSQL, a web MVP and in-browser/WebAssembly computation. It doesn't
fix languages or frameworks. Constraints:

- A single **canonical crypto implementation** must run in the browser (vault origin), in an offline recovery tool (FR-5.3), and possibly in a native companion later (ADR-006).
- The coverage gap must run on the client (FR-2.1).
- The vault origin must have a tiny, auditable dependency set (§5.6).
- The team works in Python and Java and is building up Rust.

## Decision

| Layer | Choice | Rationale |
|---|---|---|
| Crypto core | **Rust** crate `vault-core` with RustCrypto XChaCha20-Poly1305, `hkdf`, `sha2`, `x25519-dalek` or HPKE, a reviewed threshold/Shamir crate (to be selected during security review) and `zeroize` | One implementation for WASM and native. Memory safety. External crypto review is required before Phase 1 exit |
| Browser bindings | `vault-wasm` via `wasm-bindgen` | Keeps crypto out of JS. JS handles UI only |
| Risk engine | Rust crate `risk-engine` → WASM | Versioned formulas, deterministic and testable |
| Recovery tool | Rust `recovery-cli` (no network) | Independent recovery after service sunset |
| Web apps | **Svelte + TypeScript + Vite**, static SPA per origin | Static output enables strict CSP, hash-pinned scripts and reproducible builds. No SSR on the vault origin |
| Backend services | **Rust (axum + sqlx + tokio)** | Shares types with `vault-core` (envelope parsing/validation), small attack surface |
| Database | **PostgreSQL 16+**, a separate DB or role per domain | PRD §6 |
| Object storage | S3-compatible, private buckets, no predictable URLs | Encrypted attachments and evidence |
| Escrow key protection | Cloud KMS/HSM | R-08 |
| Messaging | Transactional outbox → relay. CDC (Debezium → Kafka) only if volume justifies it | Simple first. No vault-derived payloads on the bus |
| Analytics | Allow-listed events → separate store (warehouse or lakehouse later) | NFR-PRIV-01 |
| CI | GitHub Actions: fmt, clippy, tests, `cargo-deny`/`cargo-audit`, `npm audit`, SBOM (CycloneDX), CSP check, signed release manifest | §5.6 |

The ad-catalog signing key is kept offline, with its public key pinned in the vault build. Key ownership, rotation and revocation procedures remain a security gate (R-26).

## Consequences

- Rust is on the critical path. `vault-core` needs an external crypto review before Phase 1 exit.
- WASM bundle size and init time must meet NFR-PERF-01 (P95 < 2 s).
- If the backend goes to the JVM, the envelope validation rules must be specified in `docs/formats/` and tested against shared golden vectors.
