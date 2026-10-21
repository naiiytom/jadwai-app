# Requirements Traceability

Maps every PRD requirement to its component, phase and verification. **Update this
table in the same PR that adds, changes or implements a requirement.**

Status: `planned` → `designed` (ADR accepted) → `implemented` → `verified` (AC test green in CI).

## Functional requirements

| FR | Title | Component(s) | Phase | Verified by | Status |
|---|---|---|---|---|---|
| FR-1.1 | Tier & commercial entitlement | identity, web-app | 1 | — | planned |
| FR-1.2 | Asset & liability mapping | web-vault, vault-core | 1 | — | planned |
| FR-1.3 | Thai will preparation (1656/1657) | web-vault, config/legal-templates | 1 | AC-09, AC-10 | planned |
| FR-1.4 | Emergency Capsule & Living Will separation | web-vault, vault-core | 1 | — | planned |
| FR-1.5 | Recovery readiness setup | web-vault, recovery-orchestrator | 1 | — | planned |
| FR-1.6 | Owner account & device recovery | identity, vault-core | 1 | AC-13, AC-14 | planned |
| FR-1.7 | Verifier identity assurance | identity, recovery-orchestrator | 0/2 | AC-18 | planned |
| FR-2.1 | Local coverage gap | risk-engine (WASM) | 1 | NFR-PRIV-01 | planned |
| FR-2.2 | Contextual native ad matching | web-vault, config | 3 | AC-07 | planned |
| FR-2.3 | Privacy-Limited Quote | web-lead, lead-isolation | 3 | AC-08 | planned |
| FR-3.1 | Agent relationship portal | web-partners, partner-registry | 3 | AC-06 | planned |
| FR-3.2 | Corporate HR hub | web-partners, partner-registry | 3 | AC-06 | planned |
| FR-4.1 | Dual-path trigger | recovery-orchestrator, notification | 2 | AC-04 | planned |
| FR-4.2 | Two-tier access control | vault-core | 1 | AC-01 | planned |
| FR-4.3 | Unlock approval & release delay | recovery-orchestrator | 2 | AC-03, AC-04 | planned |
| FR-4.4 | Bereavement dashboard | web-vault, config/benefits | 2 | AC-11 | planned |
| FR-4.5 | OIC My Legacy handoff | web-vault | 2 | — | planned |
| FR-4.6 | Disputed unlock & freeze | recovery-orchestrator | 2 | AC-15 | planned |
| FR-4.7 | Temporary delegated access | vault-core, recovery-orchestrator | 2 (pilot) | AC-19 | planned |
| FR-5.1 | Card / recovery package lifecycle | recovery-orchestrator, vault-core | 2 | AC-05 | planned |
| FR-5.2 | Verifier lifecycle | recovery-orchestrator | 1/2 | AC-03 | planned |
| FR-5.3 | Portability & service sunset | vault-core, recovery-cli | 0/2 | AC-17 | planned |
| FR-5.4 | Closure & crypto-erasure | vault-api, recovery-orchestrator | 2 | AC-20 | planned |
| FR-5.5 | Multi-device sync & rollback | vault-api, vault-core | 1 | AC-16 | planned |
| FR-5.6 | Document provenance | vault-api, web-vault | 1 | — | planned |

## Non-functional requirements

| NFR | Component(s) | Verified by | Status |
|---|---|---|---|
| NFR-SEC-01 Crypto separation | vault-core | AC-01 test | planned |
| NFR-SEC-02 No nonce reuse | vault-core | property test | planned |
| NFR-SEC-03 Authentication | identity | e2e | planned |
| NFR-SEC-04 Origin isolation | web-vault, infra | AC-07 CSP test | planned |
| NFR-SEC-05 Auditability | security-events | INV-19 | planned |
| NFR-SEC-06 Rollback protection | vault-api, vault-core | AC-16 | planned |
| NFR-PRIV-01 Data minimization | all, analytics | schema allow-list tests | planned |
| NFR-REL-01 Durability | infra | restore exercise | planned |
| NFR-REL-02 RPO/RTO | infra | DR exercise | planned |
| NFR-REL-03 Portable recovery | recovery-cli | AC-17 | planned |
| NFR-PERF-01 Vault UX | web-vault, vault-wasm | benchmark | planned |
| NFR-A11Y-01 Accessibility | all apps | axe + manual audit | planned |
| NFR-I18N-01 Localization | all apps, config | copy review | planned |
| NFR-COMP-01 Retention | all services | retention jobs + audit | planned |
| NFR-ID-01 Verifier IAL | recovery-orchestrator | AC-18 | planned |
| NFR-DUR-01 Service exit | ops runbook | annual exercise | planned |
| NFR-DATA-01 Legacy freshness | web-vault | unit tests | planned |

## Acceptance criteria → invariant

| AC | Invariant | AC | Invariant |
|---|---|---|---|
| AC-01 | INV-02 | AC-11 | INV-23 |
| AC-02 | INV-03, INV-04 | AC-12 | INV-12 |
| AC-03 | INV-06 | AC-13 | INV-12 |
| AC-04 | INV-07 | AC-14 | INV-13 |
| AC-05 | INV-18 | AC-15 | INV-09 |
| AC-06 | INV-17 | AC-16 | INV-14 |
| AC-07 | INV-10 | AC-17 | INV-20 |
| AC-08 | INV-16 | AC-18 | INV-21 |
| AC-09 | — (copy test) | AC-19 | INV-15 |
| AC-10 | — (copy test) | AC-20 | INV-22 |
