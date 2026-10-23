# Owner account and device settings

**Status:** Proposed design for Phase 1; no production UI is implemented.

## Scope

This screen is the owner's authenticated place to review trusted devices and manage account-recovery actions described by FR-1.6. It is not a general profile or billing screen. Setup progress belongs to the first-time setup flow; legal documents and beneficiary workflows remain separate.

## Information shown

- Trusted devices with the device label, enrollment time, and current status (active or revoked) when those values are available from the trusted-device record.
- Recovery-kit status without displaying or retrieving recovery material.
- Recent security events for device enrollment and revocation, without vault content or key material.

Do not infer that a device is compromised, that historical ciphertext has become unreadable, or that a recovery action succeeded until the authoritative service/device state confirms it.

## Actions and guards

| Action | Required behavior |
|---|---|
| Enroll a device | Require phishing-resistant authentication/MFA according to the approved policy; notify the owner through a configured channel and record a security event (FR-1.6, AC-14). |
| Revoke a lost device | Confirm the target device, revoke its credential, and show the resulting status. Revocation must prevent that device from decrypting newly re-wrapped material (FR-1.6, AC-14, INV-13). |
| Replace a passkey or recover the owner account | Follow owner-held recovery only. The UI must not offer a support/admin reset that reveals plaintext or Tier keys (FR-1.6, AC-13, INV-12). |
| Rotate recovery material | Do not expose the secret in settings. Require the approved owner-held recovery and rotation flow; show completion only after the re-wrap/rotation result is confirmed. |

These controls depend on accepted decisions for ADR-002, ADR-005, and ADR-013. This document does not select a passkey API, key-wrapping scheme, server contract, or revocation protocol.

## States the UI must handle

- Loading and unavailable device data.
- No trusted devices beyond the current device.
- Active and revoked devices.
- A pending action, a confirmed result, and a recoverable failure.
- A recovery action that cannot continue because owner-held recovery material is unavailable.

Never report a failed request as a successful revocation or rotation. A retry must not silently create duplicate enrollment or rotation actions.

## Accessibility and privacy

- Identify each device and its status in text; do not use color alone.
- Make confirmation and failure states available to assistive technology and keyboard users.
- Do not display recovery secrets, Tier keys, vault plaintext, or sensitive financial data on this screen or in its telemetry.

## Traceability

- PRD FR-1.6, AC-13, AC-14
- Security invariants INV-12 and INV-13
- Dependencies: ADR-002, ADR-005, ADR-013

## Deferred

Implementation waits for Phase 0 design freeze and accepted dependencies above. Counsel-dependent legal copy, account identity proofing, and provider-specific authentication setup are outside this screen's contract.
