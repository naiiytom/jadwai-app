# jadwai-app

**Digital Legacy & Coverage Gap Vault (Thailand market)**: a client-side encrypted
vault for assets, debts, insurance policies, benefits and final wishes. Families can
reach it in an emergency or after a death, but only under conditions the owner set
in advance. The server never needs to read the vault.

> **Status: Phase 0, legal/security design freeze.** The repo has documentation and
> an empty skeleton only. There is no production code yet. The source of truth is
> [PRD v1.1.1](docs/prd/PRD-v1.1.1.md) (Draft / Architecture Review Candidate).

---

## What it does

| Area | Summary | PRD |
|---|---|---|
| Freemium vault | Map assets, liabilities, insurance and entitlements. Free tier covers 5 items. Lifetime Vault Pass and an optional Continuity Service are sold separately. | FR-1.1, FR-1.2 |
| Thai will preparation | §1657 *Handwritten Will Guide* (guidance only) and §1656 *Ordinary Will Document Pack* with a 2-witness signing guide | FR-1.3 |
| Emergency Capsule (Tier 1) | Emergency contacts, self-reported health summary, Living Will (NHA §12) reference, funeral wishes | FR-1.4, FR-4.2 |
| Estate Vault (Tier 2) | Assets, liabilities, insurance, will, claim documents. Released by 2-of-3 threshold + death verification + 48 h release delay | FR-4.2, FR-4.3 |
| Coverage gap | Computed **only on the client** (WASM). Raw amounts never leave the device | FR-2.1 |
| Privacy-Limited Quote | Lead created only by explicit user request, with a versioned consent receipt and a lead domain isolated from the vault | FR-2.3 |
| B2B2C | Agent relationship portal and Corporate HR benefits hub. Partners **never** see plaintext | FR-3.1, FR-3.2 |
| Bereavement | Stage-based dashboard (first 72 h → estate settlement), OIC *My Legacy* guided handoff | FR-4.4, FR-4.5 |
| Recovery lifecycle | Revocable NFC/QR recovery card, verifier lifecycle, portable encrypted export, crypto-erasure on closure | Module 5 |

## Non-negotiables (short version)

1. Tier 1 recovery can **never** produce material that decrypts Tier 2. Hiding something in the UI is not access control.
2. Each object gets its own AEAD nonce, and AAD binds `vault_id`, `tier`, `object_type`, `schema_version` and `key_version`. No custom crypto.
3. Inactivity alone never unlocks Tier 2. Approvals are scoped to one request and expire. `SECURITY_FREEZE` never ends on a timer.
4. The vault origin loads no third-party JS, analytics, ads or session replay.
5. No support or admin path can produce plaintext or Tier keys.
6. Plaintext, keys and raw financial values never appear in logs, telemetry or analytics.

The full normative list is in [docs/security/INVARIANTS.md](docs/security/INVARIANTS.md).

## Repository map

```text
.
├── CLAUDE.md                 # Guidance for AI coding agents (read first)
├── docs/
│   ├── prd/                  # PRD v1.1.1 (Markdown conversion)
│   ├── CONTEXT.md            # Domain, personas, glossary, Thai legal context
│   ├── ARCHITECTURE.md       # System architecture & zero-knowledge model
│   ├── ROADMAP.md            # Phase 0–3 plan with exit criteria
│   ├── TRACEABILITY.md       # FR / NFR / AC → component → phase
│   ├── architecture/         # Data model, state machines, leads & partners, review notes
│   ├── security/             # Invariants & threat model
│   └── adr/                  # Architecture Decision Records (ADR-001…017)
├── apps/                     # Web frontends, one per origin (planned)
├── crates/                   # Rust: vault-core crypto, WASM bindings, risk engine, recovery CLI (planned)
├── services/                 # Backend services (planned)
├── db/                       # PostgreSQL migrations (planned; schema still logical)
└── config/                   # Versioned benefit tables & legal templates (source + effective date)
```

## Agreed stack (ADR-016)

- **Crypto & client logic:** Rust `vault-core`, compiled to **WASM** for the browser and to native code for an independent recovery CLI (FR-5.3: export bundles must stay recoverable even if the platform shuts down).
- **Web:** Svelte + TypeScript + Vite static SPAs, one per origin (`www`, `app`, `vault`, `partners`, `lead`), strict CSP + Trusted Types.
- **Backend:** Rust (axum + sqlx) on **PostgreSQL**. The append-only security event store uses a hash chain. A transactional outbox feeds notifications.
- **Analytics:** allow-listed, schema-validated events only, in a domain physically separate from vault ciphertext.

See [ADR-016](docs/adr/ADR-016-implementation-stack.md). The stack is agreed; the repo remains Phase 0 and has no production code.

## Getting started

Nothing builds yet. To contribute during Phase 0:

1. Read [CLAUDE.md](CLAUDE.md), [docs/CONTEXT.md](docs/CONTEXT.md) and [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).
2. Pick an open ADR or security gate in [docs/adr/](docs/adr/README.md) or a Phase 0 item in [docs/ROADMAP.md](docs/ROADMAP.md).
3. Follow [CONTRIBUTING.md](CONTRIBUTING.md). Security-sensitive changes need the PR checklist.

## Legal notice

This repository is a product and technical specification, **not legal advice**.
Will templates, Living Will copy, the insurance-lead model, the legal directory model
and benefit amounts all need Thai counsel review (and regulator confirmation where
needed) before production. See PRD §8 and §15.

## License

[MIT](LICENSE)
