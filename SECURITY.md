# Security Policy

## Reporting a vulnerability

Please **don't** open a public issue. Report privately through GitHub's
"Report a vulnerability" (Security Advisories) on this repository.

Include affected components, reproduction steps and impact. We aim to acknowledge within
3 business days.

## Scope highlights

Especially interesting:

- anything that lets Tier 1 recovery reach Tier 2 material
- nonce reuse or AAD bypass
- replaying approvals or bypassing the release delay or `SECURITY_FREEZE`
- third-party code execution in the vault origin
- plaintext or keys in logs or telemetry
- linking leads back to vaults

Design-level security docs: [docs/security/](docs/security/).
