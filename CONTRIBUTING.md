# Contributing

## Before you start

1. Read [CLAUDE.md](CLAUDE.md) (it applies to humans too), [docs/CONTEXT.md](docs/CONTEXT.md) and
   [docs/security/INVARIANTS.md](docs/security/INVARIANTS.md).
2. Find the PRD requirement your change implements. If none exists, raise it before writing code.

## When an ADR is required

Write an ADR first if a change touches any of these:

- crypto format, cipher, KDF, key hierarchy or wrappers
- recovery topology, escrow, card construction, state machine transitions
- origin boundaries, CSP, third-party code in any app
- data-domain boundaries (anything that could link the lead/partner/analytics domains to the vault)
- user-facing claims about privacy or "zero-knowledge"

## Branches & commits

- Use short-lived feature branches and small PRs.
- Commit messages: imperative mood. Reference PRD IDs, e.g. `vault-core: bind key_version in AAD (FR-4.2, AC-02)`.

## Pull requests

- Fill in the PR template, including the security checklist.
- Update `docs/TRACEABILITY.md` when requirement status changes.
- Changes to `crates/vault-core`, `apps/web-vault`, `services/recovery-orchestrator` or `docs/security`
  need Security review.

## Test data

Synthetic only. No real names, Thai national ID numbers, policy numbers, account numbers or
health data, even in screenshots.
