# Phase 0/1 Setup and Copy Test Plan

This plan covers the setup-state, settings, and copy workstreams. It defines verification
targets from the existing PRD; it does not add product requirements or select a test stack.
ADR-016 and ADR-017 are still Proposed, and the repository has no implemented product code
or test tooling. These checks become executable when the relevant spike or implementation
exists.

## Checks by phase

| Workstream | Phase 0: design/spike evidence | Phase 1: CI and acceptance evidence | Requirements |
|---|---|---|---|
| Setup state | For proposed ADR-018 milestones, verify the ordered path `NOT_STARTED → ACCOUNT_READY → VAULT_CREATED → TIERS_CONFIGURED → RECOVERY_READY → DEVICE_READY → VAULT_READY`; verify interruption resumes from the last completed milestone. Verify progress alone never authorizes access, recovery or key release. Completion guards remain provisional until ADR-001/002/005/013 decisions are accepted. | Test every accepted transition and its failed/incomplete guard, plus resume after interruption. Confirm optional content is not required for `VAULT_READY`, and progress cannot bypass owner-held recovery or device security checks. Keep fixtures synthetic. | FR-1.6; AC-13, AC-14; INV-12, INV-13 |
| Settings UI | Review the proposed settings contract against the accepted recovery/authentication decisions before implementation. | Exercise each specified view state: loading; device data unavailable; no trusted devices beyond the current device; active and revoked devices; pending action; confirmed result; recoverable failure; and recovery blocked because owner-held material is unavailable. Guard checks: enrollment requires the approved phishing-resistant authentication/MFA policy and emits an owner notice/security event; revocation targets the confirmed device and prevents it decrypting newly re-wrapped material; passkey replacement/account recovery stays owner-held and offers no support/admin reset that yields plaintext or tier keys; recovery rotation exposes no secret and reports completion only after confirmed rotation/re-wrap. Failed requests must not display success, and retry must not silently duplicate enrollment/rotation. | FR-1.6; AC-13, AC-14; INV-12, INV-13 |
| Settings accessibility | In the proposed UI states, device identity and status are expressed in text rather than color alone; confirm/failure states are available to keyboard and assistive-technology users. | Verify keyboard access and focus behavior for all actions, accessible names/status announcements for pending, confirmed and failure states, and a manual WCAG 2.2 AA audit for the in-scope core flow. | NFR-A11Y-01 |
| Copy | Obtain Thai counsel's review/sign-off for will templates and Living Will copy before treating legal wording as approved. Record the reviewed version and source in the counsel memo. | Add content/schema checks for required version, locale, source and effective date. After counsel sign-off, check the §1657 guide warning and handwritten requirements, §1656 witness requirements and checklist warnings, age gate, and separation of the Living Will from generic DNR/preferences. Review Thai-first presentation and any English locale for parity. | FR-1.3, FR-1.4; AC-09, AC-10; INV-23; NFR-I18N-01 |

## Phase 0 security spikes

These are the roadmap's throwaway, non-shipping spike checks. They are prerequisites for
design evidence, not substitutes for Phase 1 CI coverage:

- Envelope encrypt/decrypt binds `vault_id`, tier, object type, schema version and key
  version through AAD; mutation of any bound field fails decryption. Generated tests must
  show nonce uniqueness for a key and that a Tier 1 fixture cannot decrypt Tier 2 data.
  (AC-01/02; INV-02/03/04.)
- Exercise the selected threshold-recovery option(s) with synthetic fixtures, including
  insufficient shares and wrong/revoked recovery material. Record which recovery cases
  the prototype does not cover. (FR-1.6; ADR-002/003.)
- The vault-origin CSP/Trusted Types skeleton fails when an unauthorized script source is
  introduced. (AC-07; INV-10.)

## Not verifiable yet

- ADR-018 and the settings UI contract are Proposed design only. Their listed states and
  guards are test-plan inputs, not accepted implementation requirements; executable checks
  depend on the ADR/dependency decisions and Phase 0 gates.
- ADR-016/017 must be accepted before choosing test frameworks, commands or CI wiring.
- Counsel-dependent wording cannot be marked passing from automated string checks alone;
  the Phase 0 counsel memo is required evidence for legal-copy approval.
- Phase 1 exit also requires external crypto review, vault-origin penetration testing,
  core-flow accessibility evidence and the roadmap's performance target. This plan does
  not replace those release gates.
