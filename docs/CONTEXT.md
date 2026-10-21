# Product & Domain Context

Background for anyone (human or agent) joining the project. The requirements are in
[PRD v1.1.1](prd/PRD-v1.1.1.md). This document explains *why* they exist and gives
the vocabulary.

## 1. Problem

Many Thai households have no single "map" of their assets, debts, insurance policies,
benefits and final wishes. After an emergency or a death, the family searches through
paper and calls banks, insurers, the Social Security Office, employers and the Provident
Fund (PVD), all while under time pressure. People also have **subscription fatigue** for apps
they don't open every day, so a consumer-only monthly model meets resistance (PRD §1.1).

## 2. Thesis

1. **Encrypted vault, client-side.** The platform stores ciphertext and wrapped keys,
   not plaintext.
2. **Recovery designed in.** The owner sets in advance who can open what, and when.
3. **Monetize without reading the vault.** Contextual matching runs on the client.
   Leads exist only after explicit consent. B2B2C sponsorship and white-label cover the rest.
4. **Trust is the moat.** No ad SDKs, trackers or session replay in the decrypted context.

## 3. Personas & access boundaries

| Role | Wants | Can see |
|---|---|---|
| **Vault Owner** | Organize everything and define recovery | Plaintext on trusted devices. Manages keys and verifiers |
| **Emergency Contact / Verifier** | Confirm an event and help family | Nothing from Tier 2 until threshold + release delay. Can't act alone |
| **Heir / Family** | Find information and work through checklists | Only the tier that was unlocked |
| **Insurance Agent / FA** | Retain clients, act as contact point | Relationship metadata only. Not a default share holder |
| **Corporate HR** | Explain benefits, help with beneficiary readiness | Only the HR program data the employer controls. Never the personal vault |
| **Service Partner** | Receive consented leads | The minimum lead payload after assignment |
| **Platform Ops / Support** | Billing, delivery, security events | No plaintext and no Tier keys. Can't bypass crypto policy |
| **Temporary Delegate** | Help while the owner is incapacitated | The delegated Tier 1 subset only. No Tier 2 |

## 4. Commercial model (hypotheses, PRD §11)

| Product | Price (hypothesis) | Notes |
|---|---|---|
| Free | 0 | 5 items, basic will guide, 1 emergency contact, email notifications |
| Lifetime Vault Pass | 990 THB one-time | Unlimited items, attachments, first recovery card, document packs. **Doesn't** include recurring telecom |
| Continuity Service | 299 THB/year | Heartbeat orchestration, LINE/SMS/IVR escalation. Cancelling never removes vault access |
| Agent SaaS | 490–990 THB/month | Invites, branding, relationship dashboard |
| Leads / directory | Fixed CPL or fee | **No % of premium or legal fee** until licensing/ethics are confirmed |
| Corporate HR | Per employee / annual | Employer data strictly separated from the personal vault |

## 5. Thai legal & regulatory context

> These summaries are for engineering orientation only. They aren't legal advice. Copy
> and templates need Thai counsel sign-off (Phase 0).

| Topic | What engineering must know | PRD |
|---|---|---|
| **CCC §1656**: ordinary will (พินัยกรรมแบบธรรมดา) | In writing, **dated at the time it is made**, signed by the testator in front of **≥ 2 witnesses present together**, who certify the signature at that time. Erasures, additions or other alterations are invalid unless made in the same form. We produce a document pack plus a signing checklist | FR-1.3, AC-10 |
| **CCC §1657**: holographic will (พินัยกรรมเขียนเองทั้งฉบับ) | The testator handwrites the entire text, the date and the signature. Alterations are invalid unless handwritten **and signed** by the testator. §9 para 2 doesn't apply, so a **fingerprint or mark can't replace the signature**. We produce only a **guide or overlay**, never a printable will | FR-1.3, AC-09 |
| **CCC §1653**: witness/writer can't benefit | The writer of the will or a witness, **or their spouse**, can't take property under that will. The signing checklist must warn about this | FR-1.3 |
| **CCC §1670**: who can't be a witness | Minors, persons of unsound mind or quasi-incompetent by court order, and persons who are deaf, mute or blind in both eyes. Belongs in the §1656 checklist | FR-1.3 |
| **CCC §25**: testamentary age | A minor may make a will once they are **15 years old**. The will flow should check age | FR-1.3 |
| **National Health Act B.E. 2550 §12**: Living Will (หนังสือแสดงเจตนา) | A written statement refusing health services that only prolong dying at the end of life or prolong suffering. Procedures are set by the **Ministerial Regulation B.E. 2553**, in force from 2011. It lists the required contents (personal details, date, witnesses, services refused) and lets the person **appoint a proxy**. **Not** a generic DNR. Link to the official e-Living Will | FR-1.4, §8.4 |
| **PDPA B.E. 2562 §26** | Sensitive data: race, ethnic origin, political opinions, cult/religious/philosophical beliefs, sexual behavior, criminal records, health, disability, trade union info, genetic and biometric data, plus any category the PDPC adds. Processing generally needs **explicit consent**, and the exceptions are narrow. Zero-knowledge doesn't remove PDPA duties for metadata, contacts, logs and consent | §8.1 |
| **OIC (คปภ.)**: licensing | Lead fees may still count as insurance solicitation or brokerage. **The OIC guidelines on digital insurance content by unlicensed persons (B.E. 2569, in force 24 Jul 2026)** reserve for licensees: soliciting or facilitating contracts, personalized suitability advice, **links that facilitate contract formation**, and **performance-based compensation tied to policies or premiums**. They also require sponsorship and license-status disclosure. Counsel must confirm how this applies to contextual cards and CPL (REVIEW-NOTES R-16) | §8.2, FR-2.2, FR-2.3 |
| **OIC Connect – My Legacy** | Heirs can search a deceased person's policies in the OIC Connect super app, with identity verified through **ThaiD** (launched mid-2026). No public API is documented, so we hand off to it rather than integrate | FR-4.5 |
| **Social Security (§33/§39)** | Non-work-related death, with ≥ 1 month of contributions in the 6 months before the month of death: **funeral grant 50,000 THB**, paid to whoever arranged the funeral. **Death compensation** goes to the written nominee (otherwise split equally among spouse, parents and children): 2 months' average wage for 36–119 contribution months, 6 months' for ≥ 120. **§40** benefits depend on which option the person chose (sources disagree, so verify). A **work-related** death falls under the Workmen's Compensation Fund instead (funeral 50,000 THB since Jul 2021). Amounts live in versioned config with a source and an effective date | §8.5, AC-11 |
| **Lawyer directory** | Fixed directory fee only. No cut of legal fees | §8.3 |
| **Delegated access** | It's an authorization feature in the product. It is **not** a power of attorney, court order or medical authority | §8.6 |

## 6. Glossary

| Term | Meaning |
|---|---|
| **Tier 1 / Emergency Capsule** | Emergency subset, encrypted under `K_TIER1_EMERGENCY` |
| **Tier 2 / Estate Vault** | Estate data, encrypted under `K_TIER2_ESTATE`, an independent key |
| **Envelope** | Ciphertext + `crypto_version`, `cipher_suite`, `nonce`, `key_version`, AAD metadata |
| **AAD** | Additional authenticated data that binds a ciphertext to its identity and context |
| **Key wrapper** | A Tier key encrypted under another key (owner, recovery or escrow) |
| **Share A / B / C** | Tier 2 threshold components: Platform Escrow / Primary Trusted Person / Independent Backup |
| **Recovery package** | Encrypted, opaque blob on the NFC/QR card, plus `card_id` and `generation`. Never a raw share |
| **Generation** | Monotonic version of a card or share set. Reissuing invalidates the earlier generations |
| **Unlock request** | A server-side state machine instance for one recovery attempt. Approvals are scoped to it |
| **RELEASE_DELAY_48H** | A policy delay enforced by the orchestrator. It's **not** a cryptographic time-lock |
| **SECURITY_FREEZE / DISPUTED** | A non-terminal hold that blocks release until explicit resolution |
| **Heartbeat** | Periodic owner check-in. Missing one starts escalation, **never** an unlock |
| **IAL-0…3** | Verifier identity assurance level, kept separate from the authentication method |
| **Readiness Score** | Client-side completeness/freshness score for recovery setup |
| **Legacy Freshness** | Whether contacts, policies, cards and reviews are within their freshness windows |
| **Privacy-Limited Quote** | A consented, minimum-payload lead request (formerly "Anonymous Quote") |
| **Consent receipt** | Versioned proof of consent: text version, purposes, granted, expiry, revoked |
| **Beneficiary Planning & Status Hub** | Tracks `PREFERENCE_ONLY → … → CONFIRMED_BY_PROVIDER`. It never claims to make the change |
| **Provenance status** | `SELF_DECLARED, USER_UPLOADED, PROVIDER_VERIFIED, OFFICIAL_SOURCE, SUPERSEDED, EXPIRED` |
| **Portable recovery bundle** | Self-describing encrypted export that can be recovered without the production API |
| **MRTA** | Mortgage Reducing Term Assurance |
| **PVD** | Provident Fund (กองทุนสำรองเลี้ยงชีพ) |
| **ThaiD** | Thai government digital identity app (DOPA) |

## 7. Explicit non-goals (v1.1.1)

- Not a bank, broker, custodian or crypto custodian.
- No bank passwords, OTP seeds, private keys or seed phrases as supported data types.
- A beneficiary preference in the app is **not** a beneficiary change with a provider.
- No guarantee that generated templates are legally valid for every set of facts.
- Inactivity is never treated as proof of death.
- No percentage commissions on premiums or legal fees.

## 8. Success metrics

Vault Activation (≥ 3 items + emergency contact) · Legacy Readiness · Coverage Insight
Completion (no raw values server-side) · Consent-to-Lead · Recovery Success (no support
override, no unauthorized disclosure) · Agent Retention · Security KPI (0 plaintext leaks,
100 % of sensitive actions audited) · Legacy Freshness. See PRD §1.4.
