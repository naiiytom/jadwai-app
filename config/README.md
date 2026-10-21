# config/

Versioned, sourced content that can change **without redeploying vault crypto** (AC-11).

| Dir | Content |
|---|---|
| `benefits/` | Social Security / PVD / employer benefit tables, e.g. funeral grants (eligibility-dependent) |
| `legal-templates/` | §1656 document pack, §1657 handwritten guide, Living Will references (counsel-approved versions only) |

Every entry MUST include at least:

```yaml
id: sso-funeral-grant
version: 1
locale: th-TH
effective_date: 2026-01-01
source_url: https://www.mol.go.th/
reviewed_by: <counsel/owner>
reviewed_at: 2026-10-01
```

Never present an amount as a guaranteed entitlement (PRD §8.5). Don't commit anything here
until counsel review (Phase 0.1).
