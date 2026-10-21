# crates/ (planned Rust workspace)

| Crate | Purpose | Key invariants |
|---|---|---|
| `vault-core` | Envelope (AEAD + AAD), Tier key hierarchy, wrappers, threshold recovery, revision lineage, export format | INV-02/03/04/14/20 |
| `vault-wasm` | `wasm-bindgen` bindings for `web-vault` | Keys stay inside WASM memory where possible; `zeroize` |
| `risk-engine` | Coverage gap calculation with versioned formulas and assumptions (FR-2.1) | No raw values leave the client |
| `recovery-cli` | Offline verification and recovery of portable bundles (FR-5.3) | No network access |

No custom crypto primitives. See [ADR-001](../docs/adr/ADR-001-crypto-envelope.md) and [ADR-016](../docs/adr/ADR-016-implementation-stack.md).
