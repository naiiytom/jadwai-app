# Threat Model

Baseline from PRD §5.6–§5.7, extended for implementation. Re-run the review at each
phase gate and whenever an ADR touches crypto, recovery or origins.

## Assets

1. Vault plaintext (Tier 1, Tier 2, attachments)
2. `K_TIER1`, `K_TIER2`, attachment CEKs, owner wrappers, owner recovery key
3. Threshold shares A/B/C, card activation secrets
4. Unlock request state and approvals
5. Identity data, verifier contacts, death evidence
6. Consent receipts and lead contacts
7. Integrity of the security event log
8. Integrity of the `web-vault` build (it can reach everything above)

## Adversaries

Opportunistic thief (card or device) · malicious or coerced family member · malicious verifier ·
compromised verifier account · platform insider (ops, support, DBA) · partner or agent ·
platform + partner collusion · network attacker · compromised dependency or CI · compromised
production server delivering malicious JS · regulator or legal compulsion (data minimization
limits what can be disclosed).

## Scenarios & controls

| Threat | Primary control | Residual / review | Invariant |
|---|---|---|---|
| Lost or stolen NFC card | Revocable generation, encrypted package, extra verifier auth | A photographed package plus a server compromise → rotate | INV-05, INV-18 |
| Verifier account takeover | Passkey/MFA, request-specific challenge, approval expiry | Social engineering. Multi-party threshold | INV-06 |
| Platform insider | No plaintext keys, escrow released only by the state machine, append-only events | Web build compromise remains | INV-01, INV-12 |
| Platform + partner collusion | Partner isn't a default share holder | Owner may still pick a risky topology → warn | — |
| **B + C collusion** | Independent portable authorization must enforce death approval, owner cancellation, `SECURITY_FREEZE` and the configured delay for every release path | Protocol and sunset mechanism unresolved (R-25); no timer-only release is acceptable | INV-07, INV-09 |
| Old approval replayed | Bound to `unlock_request_id` + challenge + expiry. Shares sealed to the request key | Clock and state consistency | INV-06 |
| Forged death certificate | Multi-party approval, release delay, owner cancellation, evidence review | Manual verification quality varies | INV-08 |
| Ciphertext swapping | AAD binds identity, tier and schema | Client must validate AAD | INV-04 |
| Nonce reuse | Fresh random 192-bit nonce from CSPRNG, tests and lint | Library misuse | INV-03 |
| Stale or rollback write | Revision CAS, per-device last-seen revision, conflict UX | A new device without a trusted checkpoint can miss rollback by omission (R-06) | INV-14 |
| Malicious frontend release | Origin isolation, no third-party JS, signed and reproducible builds | Can't be fully removed on the web. ADR-006 | INV-10 |
| Telemetry leak | Allow-listed schemas, no replay or DOM capture, log redaction | Developer error → CI tests | INV-11 |
| Lead re-identification | No FK to the vault, minimum payload, separate DB role | Contact info is itself identifying | INV-16 |
| Support social engineering | No reset path yields keys. Support can route only | Account takeover of the *auth* credential is still possible → notifications | INV-12, INV-13 |
| Delegate overreach | Separate Tier 1 subset wrapper, expiry, revoke | Legal misrepresentation (§8.6) | INV-15 |

## Abuse-case workshop (Phase 0 deliverable)

Owner still alive · family dispute · malicious verifier · partner compromise · lost card ·
shared family device · death certificate fraud · coercion of a trusted person · the
platform-sunset scenario.
