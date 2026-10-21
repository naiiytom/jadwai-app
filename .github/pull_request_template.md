## Summary

<!-- What and why. Reference PRD IDs (FR-x.y, NFR-…, AC-…) and ADRs. -->

## Requirements

- PRD:
- ADR:

## Security checklist

- [ ] No plaintext, keys, shares or raw financial values in logs, telemetry or errors
- [ ] No new third-party scripts or network destinations in `apps/web-vault` (CSP unchanged, or reviewed)
- [ ] Tier 1 paths still cannot reach `K_TIER2` (INV-02)
- [ ] New encryption uses a fresh nonce and full AAD (INV-03, INV-04)
- [ ] Approvals and unlock logic stay request-scoped and freeze-safe (INV-06, INV-09)
- [ ] Lead and partner domains stay unlinked from vault IDs (INV-16, INV-17)
- [ ] Legal and benefit copy comes from versioned `config/` with a source and an effective date (INV-23)
- [ ] N/A: this change doesn't touch security-relevant code

## Tests

<!-- Which invariant or AC tests cover this? -->

## Docs

- [ ] `docs/TRACEABILITY.md` updated
- [ ] ARCHITECTURE / ADR updated if the design changed
