# apps/ (planned)

One static web app per origin (PRD §5.1). Each has **its own build, CSP, cookies and service worker**.

| Dir | Origin | Notes |
|---|---|---|
| `web-marketing/` | `www.` | Public content |
| `web-app/` | `app.` | Account, billing, onboarding shell. No vault keys |
| `web-vault/` | `vault.` | Decrypted UI + `vault-wasm`. **First-party code only**, strict CSP + Trusted Types (AC-07) |
| `web-partners/` | `partners.` | Agent / HR portal, metadata only (AC-06) |
| `web-lead/` | `lead.` | Consent + Privacy-Limited Quote (AC-08) |

Stack: [ADR-016](../docs/adr/ADR-016-implementation-stack.md) (Proposed).
