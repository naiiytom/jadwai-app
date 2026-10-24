# Domain docs

This is a single-context repository.

## Read before exploring

- `docs/CONTEXT.md` for domain terms and accepted vocabulary.
- `docs/adr/` for decisions relevant to the area being changed.
- `docs/security/INVARIANTS.md` for normative security rules.
- `docs/ARCHITECTURE.md` and `docs/architecture/` for the derived design.

## Use the glossary vocabulary

Use terms from `docs/CONTEXT.md` in issue titles, implementation plans, and tests. If a needed concept is missing, record the gap for domain modeling.

## Respect source-of-truth order

Follow the order in `CLAUDE.md`: PRD, security invariants, accepted ADRs, then derived architecture. Proposed ADRs are not binding. Flag conflicts rather than silently choosing between docs.
