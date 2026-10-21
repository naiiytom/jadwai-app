# ADR-017: Monorepo layout

- **Status:** Proposed
- **Date:** 2026-10-21

## Context

Crypto, client engines, five web origins and several services have to evolve together
while keeping strict boundaries (separate origin builds, separate data domains).

## Decision (proposed)

A single monorepo:

```text
apps/       one directory per origin; each has its own build output and CSP
crates/     Rust workspace: vault-core, vault-wasm, risk-engine, recovery-cli
services/   one directory per backend service; each has its own DB migrations
db/         shared migration tooling and the logical schema
config/     versioned content: benefits/, legal-templates/, ad-catalog/ (signed)
docs/       PRD, architecture, ADRs, security, formats
```

Rules:

- `apps/web-vault` may depend only on `crates/vault-wasm` and an allow-listed set of npm packages. Shared UI packages must not pull in analytics or error-reporting SDKs.
- Services must not import each other's DB schemas. They talk over APIs or the outbox.
- `config/` entries must include `version`, `effective_date` and `source_url`, and are validated in CI.

## Consequences

- One CI pipeline with path-based jobs.
- CODEOWNERS should require Security review for `crates/vault-core`, `apps/web-vault`, `services/recovery-orchestrator` and `docs/security`.
