**PRODUCT REQUIREMENTS DOCUMENT**

Digital Legacy & Coverage Gap Vault  
(Thailand Market)

**Product Requirements & Technical Specification**

**Revision 1.1.1**

20 October 2026

**Status: Draft / Architecture Review Candidate**

Target: Thailand - B2C Individuals, B2B2C Insurance Agents / Financial Advisors, Corporate HR

# Document Control

| **Field**        | **Value**                                                                           |
|------------------|-------------------------------------------------------------------------------------|
| Project Name     | Digital Legacy & Coverage Gap Vault (Thailand Market)                               |
| Document         | Product Requirements Document & Technical Specification                             |
| Version          | 1.1.1                                                                               |
| Status           | Draft / Architecture Review Candidate                                               |
| Revision Date    | 20 October 2026                                                                     |
| Primary Audience | Product, Engineering, Security, Legal/Compliance, Insurance Partners, Corporate HR  |
| Supersedes       | PRD v1.1                                                                            |
| Decision Gate    | Architecture + Security + Thai legal/regulatory review before implementation freeze |

## Revision 1.1.1 - Summary of Material Changes

| **Area**                           | **v1.1.1 Change**                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|
| Security boundary                  | แยก Tier 1 Emergency Key และ Tier 2 Estate Key เป็น independent cryptographic keys                                                       |
| AEAD encryption                    | เปลี่ยนจาก IV ระดับ vault เป็น per-object nonce/IV + AAD + key version                                                                      |
| Unlock approvals                   | ย้าย approval จาก verifier-level boolean เป็น approval ต่อ unlock request                                                                  |
| Trust model                        | ยกเลิก Agent เป็น default Shamir share holder; default เป็น platform + trusted person + independent backup                                 |
| Recovery card                      | QR/NFC เก็บ encrypted recovery package ไม่ใช่ raw Shamir share; รองรับ revoke/rotate/reissue                                                |
| Dead-Man flow                      | Inactivity อย่างเดียวไม่สามารถปลด Tier 2; ใช้ state machine + death verification + release delay                                            |
| Living Will                        | แยก Living Will ตาม พ.ร.บ.สุขภาพแห่งชาติ ม.12 ออกจาก generic DNR/emergency preferences                                                     |
| Will generator                     | เพิ่ม guardrail ม.1656 และ 1657 และเปลี่ยน 1657 เป็น Handwritten Will Guide                                                                  |
| Insurance discovery                | เพิ่ม guided handoff ไป OIC Connect - My Legacy หลังการเสียชีวิต                                                                              |
| Lead privacy                       | เปลี่ยน “Anonymous Quote” เป็น “Privacy-Limited Quote” และแยก consent receipt/domain                                                       |
| Corporate HR                       | เปลี่ยน beneficiary feature เป็น planning/status hub ไม่อ้างว่าเปลี่ยน beneficiary กับ provider สำเร็จ                                            |
| Commercial model                   | แยก Lifetime Vault Pass ออกจาก optional Continuity Service เพื่อรองรับต้นทุนระยะยาว                                                          |
| Web threat model                   | เพิ่ม controls สำหรับ malicious/compromised JavaScript, third-party scripts และ origin isolation                                           |
| Owner/device recovery              | เพิ่ม owner-held recovery path, trusted-device lifecycle, lost-device revocation และ key re-wrap โดยห้าม support/admin reset ที่ถอด vault ได้ |
| Disputed unlock handling           | เพิ่ม DISPUTED / SECURITY_FREEZE state; conflicting evidence, owner-alive response หรือ suspected fraud หยุด key release อัตโนมัติ             |
| Identity assurance                 | แยก authentication ออกจาก identity proofing; เพิ่ม verifier assurance levels และ policy hook สำหรับ Tier 2                                 |
| Portability / service sunset       | เพิ่ม encrypted portable recovery export และ service-exit requirement เพื่อไม่ให้ platform shutdown ทำให้ vault ที่ export แล้วกู้คืนไม่ได้            |
| Multi-device / rollback protection | เพิ่ม trusted-device model, object revision lineage และ stale/rollback rejection สำหรับ security-sensitive objects                         |
| Incapacity / closure / provenance  | เพิ่ม temporary delegated access boundary, vault closure + crypto-erasure workflow และ document provenance states แบบ phased requirements |

| **Document Notice:** เอกสารนี้เป็น product/technical specification ไม่ใช่คำปรึกษากฎหมายหรือความเห็นทางกฎหมาย ก่อนเปิดใช้งานจริงต้องผ่านการตรวจโดยที่ปรึกษากฎหมายไทยและการยืนยันรูปแบบธุรกิจกับหน่วยงานกำกับที่เกี่ยวข้องตามความจำเป็น |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

# 1. Executive Summary & Business Objectives

## 1.1 Problem Statement

คนไทยจำนวนมากไม่มี “แผนที่” ที่รวมข้อมูลทรัพย์สิน หนี้สิน กรมธรรม์ สิทธิประโยชน์ และเจตนาสำคัญไว้ในที่เดียว เมื่อเกิดเหตุฉุกเฉินหรือเสียชีวิต ครอบครัวต้องค้นเอกสารและติดต่อหลายหน่วยงานภายใต้ข้อจำกัดด้านเวลา ขณะเดียวกันผู้ใช้ทั่วไปมี subscription fatigue ต่อแอปที่ไม่ได้ใช้ทุกวัน ทำให้โมเดลรายเดือนแบบ consumer-only มีแรงต้านสูง

## 1.2 Core Product Thesis

สร้าง encrypted digital legacy vault ที่เก็บข้อมูลสำคัญแบบ client-side encryption และออกแบบ recovery ให้ครอบครัวเข้าถึงได้ภายใต้เงื่อนไขที่เจ้าของกำหนด โดยไม่ให้ platform สามารถอ่านข้อความใน vault ได้ตามปกติ จากนั้นสร้างรายได้จาก contextual monetization ที่ประมวลผลบนเครื่องผู้ใช้, explicit lead consent และ B2B2C sponsorship/white-label

## 1.3 Business Objectives

- ลด friction ในการเริ่มบันทึกทรัพย์สิน/หนี้สินด้วย Free Tier ที่ใช้งานได้จริงและไม่บังคับ subscription

- เพิ่มความพร้อมของครอบครัวเมื่อเกิดเหตุฉุกเฉินหรือเสียชีวิต โดยจัดข้อมูลตามลำดับเวลาและสิทธิ์เข้าถึง

- สร้าง coverage-gap insight โดยไม่ต้องส่งยอดสินทรัพย์/หนี้สินดิบไปยัง server

- สร้าง high-intent lead เฉพาะเมื่อผู้ใช้ร้องขอและให้ consent แยกจากการใช้ vault

- สร้างเครื่องมือ relationship layer สำหรับ licensed insurance agent / financial advisor และ corporate HR โดยไม่ให้ partner เข้าถึง plaintext vault

- รักษา trust เป็น product moat: ไม่มี ad SDK, session replay หรือ third-party tracking ภายใน decrypted vault context

## 1.4 Success Metrics

| **Metric**                  | **Definition / Guardrail**                                                                                                                         |
|-----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------|
| Vault Activation            | ผู้ใช้บันทึกอย่างน้อย 3 รายการ + ตั้ง emergency contact สำเร็จ                                                                                               |
| Legacy Readiness            | สัดส่วนผู้ใช้ที่มี verifier, recovery kit/card และ heartbeat policy ครบ                                                                                    |
| Coverage Insight Completion | สัดส่วนผู้ใช้ที่คำนวณ coverage gap ใน client สำเร็จ โดยไม่มี raw financial values ใน analytics server                                                        |
| Consent-to-Lead             | สัดส่วนผู้ใช้ที่กด Privacy-Limited Quote และให้ consent โดยชัดเจน                                                                                           |
| Recovery Success            | อัตรา recovery ที่สำเร็จโดยไม่ต้อง support override และไม่มี unauthorized disclosure                                                                       |
| Agent Retention             | active sponsored clients / agent และ monthly returning agent usage                                                                                 |
| Security KPI                | 0 plaintext vault leaks; 100% security-sensitive actions in append-only audit log                                                                  |
| Legacy Freshness            | สัดส่วน vault ที่ trusted contacts/verifiers, insurance records, recovery card generation และ recovery-readiness review ยังอยู่ใน freshness window ที่กำหนด |

## 1.5 Non-Goals / Explicit Exclusions

- ไม่เป็นธนาคาร นายหน้าซื้อขายหลักทรัพย์ ผู้ดูแลสินทรัพย์ หรือ crypto custodian

- ไม่เก็บรหัสผ่านธนาคาร OTP seed, private key หรือ crypto seed phrase เป็น supported data type ใน v1.1.1

- ไม่ถือว่า beneficiary preference ในระบบเท่ากับการแก้ผู้รับผลประโยชน์กับบริษัทประกัน/PVD/provider

- ไม่รับรองว่าพินัยกรรมที่สร้างจาก template จะใช้ได้ในทุกข้อเท็จจริง; product เป็น guided document workflow และต้องมี legal disclaimer

- ไม่ให้ inactivity เพียงอย่างเดียวเป็นหลักฐานการเสียชีวิตหรือ trigger การเปิด Tier 2

- ไม่รับค่าตอบแทนเป็นเปอร์เซ็นต์จากเบี้ยประกันหรือค่าว่าความใน v1.1.1 จนกว่าจะได้รับคำยืนยันด้าน licensing/ethics ที่เหมาะสม

# 2. User Roles & Personas

| **Role**                            | **Primary Need**                                                       | **Access Boundary**                                                                               |
|-------------------------------------|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Vault Owner                         | จัดระเบียบทรัพย์สิน/หนี้สิน/กรมธรรม์/เจตนา และกำหนด recovery                    | อ่าน/แก้ plaintext vault บน trusted client; จัดการ keys/verifiers                                    |
| Emergency Contact / Verifier        | ช่วยยืนยันเหตุและเข้าถึงข้อมูลที่ owner อนุญาต                                    | ไม่เห็น Tier 2 ก่อน threshold + release delay; ไม่มีสิทธิ์โดยลำพัง                                         |
| Heir / Family Member                | ค้นข้อมูลหลังเหตุฉุกเฉิน/เสียชีวิตและทำ checklist                                | เข้าถึงเฉพาะ tier ที่ถูกปลดล็อก; อาจไม่ใช่ verifier                                                       |
| Insurance Agent / Financial Advisor | ดูแลลูกค้า สร้าง invite และเป็น contact point                               | เห็น client relationship metadata เท่านั้น; ไม่เห็น plaintext vault; ไม่เป็น key-share holder โดย default |
| Corporate HR                        | อธิบาย benefits และช่วยพนักงานเตรียม beneficiary status                    | เห็นเฉพาะข้อมูล HR program ที่องค์กรเป็น controller/processor ตามขอบเขต ไม่เห็น personal vault             |
| Service Partner                     | รับ lead ที่ผู้ใช้ยินยอม เช่น insurance/legal/funeral                          | รับ minimum necessary lead payload; ไม่มี vault access                                               |
| Platform Ops / Support              | ดูระบบ billing, delivery, security events                               | ไม่มี plaintext vault/MVK; support override ห้าม bypass cryptographic policy                         |
| Temporary Delegate                  | เข้าถึงข้อมูลจำกัดเมื่อ owner มีภาวะไร้ความสามารถชั่วคราวตาม policy ที่ owner กำหนด | เข้าถึงเฉพาะ delegated scope/Tier 1 subset; ไม่มีสิทธิ์ Tier 2 estate vault โดยอัตโนมัติ                    |

## 2.1 Product Trust Principles

- Cryptographic authorization must match product authorization: ถ้า UI บอกว่า Tier 1 เท่านั้น กลไกกุญแจต้องทำให้ Tier 2 ถอดไม่ได้จริง

- No single commercial partner is trusted with enough material to recover Tier 2

- Consent is purpose-specific, versioned, revocable where applicable, and provable

- Sensitive computation defaults to local processing

- Every recovery path must be revocable/rotatable before an incident

- No silent downgrade from zero-knowledge claims: feature ที่ต้องเปิดเผย plaintext ต้องระบุ scope และ consent อย่างตรงไปตรงมา

# 3. Functional Requirements

## Module 1 - Freemium Vault & Onboarding (Pre-Trigger Flow)

### FR-1.1 Tier & Commercial Entitlement

| **Tier**                               | **Included**                                                                                                 | **Commercial Note**                                          |
|----------------------------------------|--------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| Free                                   | ทรัพย์สิน/หนี้สินสูงสุด 5 รายการ, basic will guide, emergency contact 1 คน, Email notification                       | ฟรีตลอดชีพสำหรับ core vault เพื่อ drive adoption                  |
| Lifetime Vault Pass - 399 THB one-time (ราคารวม VAT 7% — assumption) | Unlimited items, encrypted attachments (fair-use limit TBD), recovery kit/card initial issue (Phase 2, FR-5.1), advanced document packs, heartbeat escalation (Phase 2, FR-4.1) ผ่าน LINE/Email/push | ราคาเป็น hypothesis และ ADR-007 ยังเป็น Proposed (ไม่ผูกพัน/ยังไม่ใช้ขาย); SMS/IVR ไม่รวม; ต้นทุน/โควต้าของ provider ยังมีอยู่; "lifetime" = vault access + export (FR-5.3) ไม่ใช่การรับประกัน hosted service ตลอดไป |
| B2B Sponsored                          | สิทธิ์ตาม package ที่ agent/employer sponsor ให้                                                                   | Sponsor ไม่มีสิทธิ์ plaintext โดยอัตโนมัติ                           |

### FR-1.2 Asset & Liability Mapping

- Bank accounts: institution, masked account identifier, branch/contact guidance; ห้ามเก็บ login password

- Mutual funds / stocks / brokerage / digital assets: provider, account reference, asset class, custody location; ห้าม seed/private key

- Real estate: title deed reference, province, ownership notes, encrypted attachment

- Insurance: life, health, MRTA, group insurance, policy number, insurer, beneficiary note, agent contact

- Liabilities: mortgage, vehicle loan, personal loan, credit facilities, creditor contact, optional outstanding bracket

- Other entitlements: PVD, Social Security, employer benefits, cooperative, pension/benefit programs

### FR-1.3 Thai Will Preparation

Mode A - Civil and Commercial Code Section 1657: “Handwritten Will Guide”

- ระบบสร้าง guidance sheet / handwriting overlay เท่านั้น และต้องขึ้นชัดเจนว่าไฟล์ที่สร้างไม่ใช่ตัวพินัยกรรมฉบับสมบูรณ์ตามโหมดนี้

- ผู้ทำพินัยกรรมต้องเขียนด้วยมือตนเองทั้งหมด รวมข้อความ วัน เดือน ปี และลายมือชื่อ

- การแก้ไข/ขูดลบ/ตกเติมต้องทำตาม formalities ที่กฎหมายกำหนด; UI ต้องเตือนก่อน export

- ห้ามมี CTA ที่ทำให้ผู้ใช้เข้าใจว่า “พิมพ์แล้วเซ็น” ใช้แทน 1657 ได้

Mode B - Civil and Commercial Code Section 1656: “Ordinary Will Document Pack”

- สร้าง PDF/print pack ที่มีวัน เดือน ปี, ช่องลายมือชื่อผู้ทำพินัยกรรม และพยานอย่างน้อย 2 คน

- ขั้นตอน signing guide ต้องระบุว่าผู้ทำพินัยกรรมลงลายมือชื่อต่อหน้าพยานอย่างน้อย 2 คนพร้อมกัน และพยานลงลายมือชื่อรับรองในขณะนั้น

- ระบบเก็บ legal template version และ effective date เพื่อ trace ว่าเอกสารสร้างจาก template ใด

- ก่อน production ต้องมี Thai counsel sign-off ต่อ template และ disclaimer

### FR-1.4 Emergency Capsule & Living Will Separation

| **Data Class**                  | **Examples**                                                        | **Handling**                                                        |
|---------------------------------|---------------------------------------------------------------------|---------------------------------------------------------------------|
| Self-reported emergency info    | allergies, critical medication, major condition, emergency contacts | Tier 1; แสดงคำเตือนให้บุคลากรการแพทย์ verify independently              |
| Living Will / Advance Care Plan | หนังสือแสดงเจตนาตาม พ.ร.บ.สุขภาพแห่งชาติ ม.12                            | เก็บเป็น document + metadata; ห้ามตีความเป็น generic DNR สำหรับทุกเหตุฉุกเฉิน |
| Funeral / cultural preferences  | รูปแบบงานศพ, ศาสนา/ความเชื่อ, การลอยอังคาร                              | Tier 1; อาจเป็น sensitive personal data และต้องได้รับการคุ้มครองตาม PDPA  |

### FR-1.5 Recovery Readiness Setup

- Owner ต้องตั้ง Primary Trusted Person และ Independent Backup Trusted Person สำหรับ Tier 2 recovery

- Agent สามารถเป็น verifier/share recipient ได้เฉพาะเมื่อ owner เลือก explicit และระบบแสดง conflict-of-interest notice

- รองรับ replace verifier, rotate share, revoke lost card, reissue recovery kit และ invalidate prior generation

- แสดง Readiness Score แบบ client-side: verifier completeness, stale contact, card generation, last heartbeat, document freshness

### FR-1.6 Owner Account & Device Recovery

Owner recovery MUST NOT create a platform-admin reset path that can reveal plaintext vault content or recover K_TIER1/K_TIER2 without owner-held recovery material.

- รองรับ trusted-device enrollment, lost-device revocation, passkey replacement, owner recovery kit, recovery-key rotation และ re-wrap ของ Tier keys หลัง credential recovery

- การเพิ่มอุปกรณ์ใหม่ต้องแจ้งเตือน owner ผ่านช่องทางที่ตั้งไว้ และ security-sensitive device enrollment ต้องใช้ phishing-resistant authentication/MFA ตาม policy

- อุปกรณ์ที่ถูก revoke ต้องไม่สามารถถอดรหัสข้อมูลที่ re-key/re-wrap หลังการ revoke ได้

- Support staff อาจช่วยด้าน account routing/identity workflow ได้ แต่ MUST NOT ได้รับ recovery material ที่เพียงพอในการถอด vault

### FR-1.7 Verifier Identity Assurance

ระบบต้องแยก authentication (ควบคุม account/credential) ออกจาก identity proofing (บุคคลนั้นคือใคร) และเก็บ assurance level ของ verifier แยกจาก auth method.

- Baseline assurance levels: IAL-0 contact verified, IAL-1 owner-vouched verifier, IAL-2 regulated/government-backed identity verification where available, IAL-3 enhanced/manual review when required.

- Tier 2 policy ต้องสามารถกำหนด minimum identity assurance ได้โดยไม่ hard-code ผู้ให้บริการรายเดียว; provider integration เป็น ADR/implementation decision.

## Module 2 - Client-Side Risk Engine & Contextual Monetization

### FR-2.1 Local Coverage Gap Calculation

```text
Coverage Gap =
(Total Liabilities + Estimated Family Living Cost)
- (Liquid Assets + Existing Life/MRTA Coverage)
```

- คำนวณบน browser/WebAssembly หรือ trusted client เท่านั้นใน v1.1.1

- ห้ามส่ง raw balances, raw liabilities หรือ policy face amounts ไป analytics/ads server

- server อาจรับเฉพาะ privacy-safe event เช่น GAP_CALCULATED=true และ coarse bucket เฉพาะเมื่อ policy/consent อนุญาต

- สูตรและ assumptions ต้องมี version เพื่อ reproduce result

### FR-2.2 Contextual Native Ad Matching

- Client ดาวน์โหลด signed static ad catalog พร้อม eligibility metadata และ expiry

- Rule matching เกิดใน client; server ไม่รู้ว่า rule ใด match จนกว่าผู้ใช้กด CTA

- ตัวอย่าง Rule A: มี mortgage และไม่มี recorded MRTA/term coverage -\> แสดง educational comparison card

- ตัวอย่าง Rule B: funeral preference เกี่ยวข้องกับลอยอังคาร/alternative wreath -\> แสดง service directory card

- ห้ามฝัง third-party ad SDK, tracking pixel หรือ session replay ใน vault origin

### FR-2.3 Privacy-Limited Quote / Explicit Lead Generation

ข้อความ CTA หลัก: **“ขอข้อเสนอโดยเปิดเผยเฉพาะข้อมูลที่จำเป็น”**

- ก่อนส่ง lead แสดง purpose, partner category, fields ที่จะส่ง, retention และวิธีถอน consent/ยกเลิกการติดต่อ

- Lead payload ใช้ age bracket / gap bracket / product intent; contact ถูกเข้ารหัสและแยกจาก vault identity เท่าที่เป็นไปได้

- Lead domain ห้ามมี user_id หรือ foreign key ตรงไปยัง encrypted vault

- Consent receipt ต้องเก็บ consent text version, purposes, granted_at, expiry และ revocation state

- Partner รับข้อมูลเท่าที่จำเป็นหลัง assignment เท่านั้น

## Module 3 - B2B2C Agent Portal & Corporate HR

### FR-3.1 Agent White-Label / Relationship Portal

- Agent package 490-990 THB/month (pricing hypothesis) สร้าง invite link / sponsored entitlement ได้

- Agent Sticky Card แสดงชื่อ รูป เลขใบอนุญาตที่ verified/last-checked, โทร/LINE และองค์กร/บริษัทตามที่เปิดเผย

- Agent dashboard เห็น client relationship status เช่น invited, activated, readiness-complete, last relationship touchpoint โดยไม่เห็น vault content

- การพิมพ์ข้อมูล agent บน physical emergency card ต้อง owner opt-in

- ระบบต้องรองรับ agent relationship termination; เมื่อ terminate card/dashboard ต้อง update โดยไม่ทำลาย vault recovery

### FR-3.2 Corporate HR Bereavement & Benefits Hub

- HR สามารถ import benefit catalog เช่น group insurance, PVD, Social Security guidance, internal bereavement benefit

- ผู้ใช้เห็น “Beneficiary Planning & Status Hub” แทนการอ้างว่าระบบเปลี่ยน beneficiary ให้ provider แล้ว

- สถานะขั้นต่ำ: PREFERENCE_ONLY, DOCUMENT_GENERATED, SUBMISSION_PENDING, SUBMITTED_TO_PROVIDER, CONFIRMED_BY_PROVIDER

- ระบบต้องแยก employer-controlled data กับ private vault data ทั้ง logical access และ consent/purpose

- Employer ห้ามเข้าถึง will, asset balances, funeral preference หรือ medical information ใน personal vault เว้นแต่มีฐานกฎหมาย/consent ที่เฉพาะและออกแบบแยกต่างหาก

## Module 4 - Post-Trigger / Bereavement Flow

### FR-4.1 Dual-Path Trigger Mechanism

Path A - Heartbeat / Inactivity Escalation

```text
ACTIVE
-> HEARTBEAT_MISSED
-> OWNER_ESCALATION (LINE/Email/push, repeated reminders; interval TBD)
-> VERIFIER_CONTACTED (e.g., D+14, LINE/Email)
-> DEATH_VERIFICATION_PENDING

IMPORTANT: inactivity alone MUST NOT unlock Tier 2.
```

Path B - Heir Emergency Scan

- Heir scans NFC/QR and opens card-specific recovery route

- Card route identifies card generation and checks revocation before any recovery material is used

- Heir/Verifier completes authentication challenge; platform creates unlock_request

- Owner receives an alert on all available channels (LINE/Email/push) and can cancel during pending/release-delay state if alive

- Provisional (ADR-007 OD-4/OD-5, owners TBD): alert delivery is best-effort. Delivery failure or silence is neither evidence of death nor approval, and it never shortens or skips `RELEASE_DELAY_48H`. If no owner channel confirms delivery, release fails closed (the delay is held) rather than proceeding. Reminder timing and channel selection are defined before Phase 2 design freeze.

- Heartbeat entitlement (provisional, ADR-007 OD-6): Free = Email notifications only, no heartbeat orchestration. Lifetime = heartbeat over LINE/Email/push. Sponsored = as Lifetime while the sponsorship is active; on expiry vault access, items and export remain, and heartbeat stops unless the owner holds Lifetime (consistent with the no-destroy-access rule in FR-3.1).

### FR-4.2 Two-Tier Access Control

| **Tier**                   | **Content**                                                                                            | **Release Policy**                                                                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| Tier 1 - Emergency Capsule | emergency contacts, self-reported health summary, Living Will document reference, funeral instructions | Card recovery + designated Tier 1 verifier/auth flow; cryptographically reveals K_TIER1 only                  |
| Tier 2 - Estate Vault      | assets, liabilities, insurance, will, claim documents, encrypted attachments                           | 2-of-3 recovery approval/key shares + death verification policy + 48-hour release delay; reveals K_TIER2 only |

| **Security Invariant:** Successful Tier 1 recovery must never provide cryptographic material sufficient to derive or decrypt Tier 2. UI hiding is not considered access control. |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

### FR-4.3 Unlock Approval & Release Delay

- ทุก approval ผูกกับ unlock_request_id และ challenge เฉพาะ request

- approval หมดอายุเมื่อ request ถูก cancel/expire/complete

- 2-of-3 threshold default: Platform Escrow + Primary Trusted Person + Independent Backup Trusted Person; ต้องมี 2 components ตาม policy ที่กำหนด

- 48-hour period เรียกว่า RELEASE_DELAY_48H ไม่ใช่ cryptographic time-lock เว้นแต่มี cryptographic primitive บังคับจริง

- Owner cancellation ระหว่าง release delay ต้อง invalidate server release token/share delivery สำหรับ request นั้น

- Support staff ไม่มีปุ่ม bypass threshold; emergency override หากมีในอนาคตต้องเป็น separate policy with legal/security design review

### FR-4.4 Stage-Based Bereavement Dashboard

Stage A - Immediate Crisis (first ~72 hours)

- funeral / cultural instructions

- family/agent emergency contact actions

- benefit checklist พร้อม eligibility caveat เช่น Social Security funeral benefit ตามสถานะผู้ประกันตนและเงื่อนไขที่ใช้บังคับ

- optional at-need funeral service directory / user-requested lead

Stage B - Estate Settlement

- asset/liability interactive checklist

- claim packet builder and document status

- guided insurance discovery: policies recorded in vault + OIC Connect “My Legacy” handoff for additional policies

- PVD/employer/Social Security claim guidance with provider-specific status

- probate / estate administrator legal service directory; legal engagement and fee paid directly between client and law firm where applicable

### FR-4.5 OIC My Legacy Guided Handoff

- แสดง OIC My Legacy เป็น official external discovery route สำหรับทายาทหลังเสียชีวิต

- v1.1.1 ไม่สมมติว่ามี public integration API; ใช้ guided handoff/deep link + checklist

- เก็บเพียง status ว่า user/heir เปิด flow หรือบันทึกผลด้วยตนเอง เว้นแต่มี official API/agreement ในอนาคต

- ข้อความ product ต้องไม่อ้างว่า vault เป็นฐานข้อมูลกรมธรรม์ของประเทศหรือแทนระบบ คปภ.

### FR-4.6 Disputed Unlock & Security Freeze

Unlock request ต้องเข้าสู่ DISPUTED / SECURITY_FREEZE เมื่อพบ conflicting verifier decisions, owner-alive response, suspected forged evidence, reported coercion, card compromise หรือ high-confidence account-takeover signal.

- SECURITY_FREEZE MUST block automatic escrow/key release and MUST pause release-delay completion.

- การออกจาก SECURITY_FREEZE ต้องผ่าน explicit resolution policy, evidence review และ append-only audit event; support/admin ไม่สามารถ bypass cryptographic threshold ได้

- Competing unlock requests สำหรับ vault เดียวกันต้องถูก correlated และอาจ trigger freeze ตาม policy.

### FR-4.7 Incapacity / Temporary Delegated Access

รองรับ owner-defined temporary delegate สำหรับกรณี hospitalization, incapacity หรือเหตุฉุกเฉินที่ owner ยังไม่เสียชีวิต โดยเป็น flow แยกจาก post-death estate unlock.

- Delegated access ต้องเป็น least-privilege, time-bounded, revocable และจำกัดเฉพาะ data subset ที่ owner กำหนด; ไม่ให้ Tier 2 estate access โดยอัตโนมัติ.

- เอกสารหรือสิทธิทางกฎหมาย เช่น power of attorney/healthcare authority ต้องถูกนำเสนอเป็น document/reference status เท่านั้นจนกว่าจะผ่าน Thai legal review และ verification policy ที่เหมาะสม.

## Module 5 - Recovery Asset Lifecycle & Security Administration

### FR-5.1 Physical Card / Recovery Package Lifecycle

- QR/NFC stores opaque/encrypted recovery package + card_id + generation, not a plain reusable share

- Owner สามารถ revoke card ได้ทันทีจาก authenticated session

- Reissue increments generation and invalidates prior package server-side; app warns that photographed old material may remain a threat until key/share rotation completes

- Lost-card workflow recommends share rotation, not merely disabling the URL

- Physical card has no plaintext medical or financial data unless owner explicitly chooses a limited printed field

### FR-5.2 Verifier Lifecycle

- Invite, accept, identity/auth setup, periodic contact revalidation, revoke, replace

- No persistent has_approved_unlock flag

- Every verifier action creates append-only security event

- If verifier is an insurance agent or partner, UI requires explicit owner acknowledgement and independent backup verifier

### FR-5.3 Vault Portability & Service Sunset

Owner ต้อง export portable encrypted recovery bundle ได้โดยไม่ต้องเปิดเผย plaintext ให้ platform และ bundle ต้องมีข้อมูลเพียงพอสำหรับ independent recovery ตาม format/version ที่ document ไว้.

- Export bundle baseline: encrypted vault objects, attachments manifest, crypto/version manifest, recovery metadata, verifier/card generation summary และ human-readable recovery instructions.

- Platform shutdown/sunset MUST NOT make a previously exported, valid bundle cryptographically unrecoverable solely because production API is unavailable.

- Service-exit plan ต้องระบุ notice period, export window, escrow/key material handling, deletion timeline และ customer communication runbook ก่อน production launch.

- Lifetime Vault Pass scope (vault access + export), attachment fair-use limit, and a funded-reserve or sunset-policy decision MUST be recorded before the pass is sold (ADR-007 OD-2/OD-3, owner TBD). Phase 3 lead revenue MUST NOT be assumed to fund continuity.

### FR-5.4 Vault Closure & Cryptographic Erasure

Account closure flow ต้องรองรับ export-before-close, card/verifier revocation, destruction/revocation of wrapped keys and escrow material, ciphertext deletion ตาม retention policy และ retention เฉพาะ audit records ที่จำเป็น.

- ยกเลิก Agent sponsorship หรือเปลี่ยน employer ต้องไม่ทำลายสิทธิ์เข้าถึง vault ที่ผู้ใช้เป็นเจ้าของอยู่แล้ว.

### FR-5.5 Multi-Device Sync & Rollback Protection

Security-sensitive vault objects ต้องมี monotonic object_revision / parent_revision lineage หรือ equivalent authenticated versioning เพื่อ detect stale write, replay และ rollback.

- Client ที่ reconnect หลัง offline ต้อง conflict-check ก่อน commit; stale client MUST NOT silently overwrite newer will, verifier, recovery-policy หรือ beneficiary-status records.

- Device identity/trust metadata ต้องถูก bind เข้ากับ write audit trail โดยไม่เก็บ plaintext vault content.

### FR-5.6 Document Provenance & Integrity

Encrypted documents ควรมี provenance metadata แยกจาก content เช่น document_type, source_type, issuer/reference, document_date, content hash, supersedes_document_id และ verification status.

- Minimum statuses: SELF_DECLARED, USER_UPLOADED, PROVIDER_VERIFIED, OFFICIAL_SOURCE, SUPERSEDED, EXPIRED; verification status ต้องไม่ถูกตีความเกินหลักฐานที่มี.

# 4. Core User Journeys

## 4.1 Owner - First-Time Setup

> **1.** Create account with phishing-resistant authentication where available (Passkey preferred; fallback MFA).
>
> **2.** Client generates cryptographic material locally; server receives ciphertext and wrapped/recovery artifacts only.
>
> **3.** Add assets/liabilities and insurance records.
>
> **4.** Configure Tier 1 Emergency Capsule separately from Tier 2 Estate Vault.
>
> **5.** Add primary trusted person + independent backup; issue recovery kit/card.
>
> **6.** Run coverage-gap calculation locally.
>
> **7.** Optionally request Privacy-Limited Quote under separate consent.
>
> **8.** If the account is entitled to heartbeat (Lifetime or active Sponsored), set frequency and notification channels (LINE/Email/push; Phase 2). Disclose that alerts can be delayed, missed or dropped (provider quotas apply) and that silence is not evidence of death or approval.

## 4.2 Heir - Emergency Scan

> **1.** Scan current-generation card.
>
> **2.** Validate card status and complete verifier authentication.
>
> **3.** If Tier 1 policy is satisfied, recover K_TIER1 only and render Emergency Capsule locally.
>
> **4.** If death is reported, create Tier 2 unlock request and collect request-scoped approvals/evidence.
>
> **5.** Enter RELEASE_DELAY_48H and notify owner/verifiers across configured channels.
>
> **6.** After threshold + delay + non-cancellation, release required escrow material to client and decrypt Tier 2 locally.
>
> **7.** Display staged bereavement dashboard and external official service handoffs.

## 4.3 Agent - Sponsored Client

> **1.** Agent passes license/profile verification and creates invite.
>
> **2.** Client joins and sees sponsor relationship disclosure.
>
> **3.** Agent sees relationship/readiness metadata only.
>
> **4.** Client may explicitly select agent as verifier, but platform recommends independent backup and does not assign agent by default.
>
> **5.** If relationship ends, sponsorship/sticky card can be removed without vault data migration or loss of recovery capability.

## 4.4 Owner - Lost Device / Account Recovery

1\. Owner initiates recovery from a new trusted client using owner-held recovery material and identity/authentication controls.

2\. System revokes or quarantines lost device credentials and records a security event.

3\. Client reconstructs/re-wraps owner access locally without platform obtaining plaintext Tier keys.

4\. Owner rotates affected recovery material when compromise is suspected and verifies current card/verifier generations.

5\. Newly enrolled device receives only current authorized wrapped keys; revoked device cannot decrypt future re-keyed data.

## 4.5 Disputed Unlock

1\. Recovery Orchestrator detects conflicting approvals/evidence or owner-alive/fraud signal.

2\. Request transitions to DISPUTED / SECURITY_FREEZE; release timer and escrow delivery stop.

3\. Evidence and verifier actions are preserved in append-only audit records without plaintext vault disclosure.

4\. Resolution follows explicit policy; unresolved dispute cannot auto-complete merely because time elapsed.

## 4.6 Owner - Temporary Delegated Access

1\. Owner pre-configures delegate scope, expiry and authentication requirement.

2\. Delegate activates temporary-access request under the designated policy.

3\. Client reveals only authorized data subset and never derives K_TIER2 solely from delegated access.

4\. Access expires/revokes automatically and creates an auditable event trail.

# 5. Technical Architecture & Zero-Knowledge Security Model

## 5.1 Architecture Boundaries

```text
www.example.com Marketing / public content
app.example.com Account, billing, onboarding shell
vault.example.com Decrypted vault UI + local crypto; NO third-party tracking
partners.example.com Agent/partner portal
lead.example.com Consent + lead isolation domain

Core services:
- Identity Service
- Encrypted Vault API / Object Storage
- Recovery Orchestrator
- Notification Service
- Lead Isolation Service
- Partner/License Registry
- Append-only Security Event Store
```

## 5.2 Client-Side Key Hierarchy

```text
Client-generated random keys

K_TIER1_EMERGENCY ---> encrypts Emergency Capsule objects
K_TIER2_ESTATE ---> encrypts Estate Vault objects

Owner session key / account recovery mechanism may WRAP both keys.
Post-death recovery policies MUST remain independent:
- Tier 1 flow can recover K_TIER1 only
- Tier 2 threshold flow can recover/unlock K_TIER2 only
```

## 5.3 Encryption Envelope

- AEAD: AES-256-GCM or XChaCha20-Poly1305 via vetted library; no custom crypto

- per-object unique nonce/IV; never one vault-wide IV

- ciphertext stored as binary/base64 envelope with crypto_version, cipher_suite, nonce, key_version and AAD metadata

- AAD binds at least vault_id, tier, object_type, schema_version and key_version

- attachments use independent content-encryption keys or chunked authenticated encryption suitable for file size

- key rotation supported without changing business identity of the vault object

## 5.4 Shamir Secret Sharing / Threshold Recovery

Default Tier 2 recovery participants:

- Share A - Platform Escrow component, encrypted at rest and released only under valid request state

- Share B - Primary Trusted Person recovery component

- Share C - Independent Backup Trusted Person recovery component

| **Trust Model Rule:** No commercial partner is a default recovery-share holder. An agent may be selected only by explicit owner choice, and policy should avoid a Platform + Agent pair becoming the only practical path without independent owner-selected trust. |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

## 5.5 Recovery Package Design

```text
QR/NFC payload (conceptual)
{
recovery_url,
card_id,
generation,
encrypted_recovery_package
}

The payload MUST NOT expose a plaintext reusable Shamir share.
Decryption/activation requires current policy + additional authentication/verification.
```

## 5.6 Web Zero-Knowledge Threat Model

A web application that receives JavaScript from the same operator has an inherent supply-chain/server-compromise risk: a malicious build could exfiltrate plaintext or keys. v1.1.1 therefore treats “zero-knowledge” as a product security objective bounded by the delivered client code, and requires hardened deployment controls.

- Strict Content Security Policy and Trusted Types where supported

- No third-party JavaScript/analytics/ads inside vault origin

- Dependency pinning, SBOM, vulnerability scanning and reviewed lockfiles

- Reproducible or independently verifiable frontend builds where practical

- Signed release artifacts / release manifest and auditable deployment pipeline

- Subresource Integrity for static third-party assets only if any are exceptionally required

- No session replay, DOM capture, remote support screen scraping or plaintext error telemetry

- Separate origins/cookies/service workers to reduce cross-domain privilege

- Key/plaintext material must never enter server logs, crash reports or analytics payloads

## 5.7 Threat Scenarios & Controls

| **Threat**                   | **Primary Control**                                                                | **Residual / Review**                                     |
|------------------------------|------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Lost/stolen NFC card         | revocable generation + encrypted recovery package + extra verifier authentication  | photographed material may require share/key rotation      |
| Verifier account takeover    | Passkey/MFA, request-specific challenge, approval expiry                           | social engineering remains; require multi-party threshold |
| Platform insider             | no plaintext key, escrow release state machine, append-only events                 | web build compromise remains higher-order risk            |
| Platform + partner collusion | partner not default share holder; independent trusted persons                      | owner can still explicitly choose risky topology; warn    |
| Replay old approval          | approval bound to unlock_request_id + nonce/challenge + expiry                     | clock and state consistency required                      |
| Death certificate fraud      | multi-party approval + release delay + owner cancellation + evidence review policy | manual verification quality varies                        |
| Ciphertext swapping          | AAD binds object identity/tier/schema                                              | requires correct AAD validation client-side               |
| Nonce reuse                  | per-object generated nonce + test/lint invariant                                   | implementation/library misuse                             |
| Malicious frontend release   | origin isolation, no third party scripts, controlled build/release, audit          | cannot be fully eliminated in ordinary web delivery       |

## 5.8 Device Trust, Sync & Rollback Model

Each authorized device receives a non-secret device identifier and authenticated device credential. Security-sensitive writes include revision lineage and writer-device reference in authenticated metadata/audit events.

- Server synchronization may coordinate opaque ciphertext revisions but MUST NOT resolve semantic conflicts by decrypting content. Client-side conflict UX is required for incompatible concurrent edits.

- Rollback protection must cover will/recovery/verifier/policy configuration even when the old ciphertext is otherwise cryptographically valid.

## 5.9 Identity Assurance Model

Authentication strength and identity assurance are separate policy inputs. Tier 2 recovery evaluates both verifier authentication and configured identity-assurance requirement.

- Identity-provider artifacts/tokens must be minimized, retention-limited and separated from vault plaintext. No single external identity provider is a cryptographic root of trust for K_TIER2.

## 5.10 Portable Recovery / Service Exit Architecture

The crypto format, manifest schema and recovery bundle version must be documented sufficiently to allow validation/recovery without relying on undocumented production-server behavior.

- Exported bundles should be self-describing, integrity-checkable and versioned; future format migrations must preserve an upgrade/recovery path.

# 6. Core Data Model (PostgreSQL) - Revision 1.1.1

หมายเหตุ: schema ด้านล่างเป็น logical baseline สำหรับ architecture review ไม่ใช่ migration-ready DDL. ชื่อ type/check constraints, partitioning, indexing และ cryptographic serialization ต้อง finalize ใน ADR/implementation design.

```text
CREATE TABLE users (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
email CITEXT UNIQUE NOT NULL,
phone_hash TEXT NULL,
tier VARCHAR(30) NOT NULL DEFAULT 'FREE',
sponsored_by_agent_id UUID NULL,
vault_status VARCHAR(40) NOT NULL DEFAULT 'ACTIVE',
inactivity_days INT NOT NULL DEFAULT 90,
last_heartbeat_at TIMESTAMPTZ NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE vaults (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
user_id UUID UNIQUE NOT NULL REFERENCES users(id) ON DELETE CASCADE,
crypto_version INT NOT NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE encrypted_vault_objects (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
tier VARCHAR(20) NOT NULL, -- TIER1 / TIER2
object_type VARCHAR(60) NOT NULL,
schema_version INT NOT NULL,
cipher_suite VARCHAR(40) NOT NULL,
nonce BYTEA NOT NULL,
ciphertext BYTEA NOT NULL,
key_version INT NOT NULL,
aad_version INT NOT NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
updated_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE key_wrappers (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
key_purpose VARCHAR(40) NOT NULL, -- TIER1_KEY / TIER2_KEY
wrapper_type VARCHAR(40) NOT NULL, -- OWNER / RECOVERY / ESCROW
key_version INT NOT NULL,
wrapped_key BYTEA NOT NULL,
metadata JSONB NOT NULL DEFAULT '{}'::jsonb,
revoked_at TIMESTAMPTZ NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE vault_verifiers (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
role VARCHAR(40) NOT NULL, -- PRIMARY_TRUSTED / BACKUP_TRUSTED / OPTIONAL_AGENT
display_name VARCHAR(255) NOT NULL,
contact_hash TEXT NULL,
contact_encrypted BYTEA NULL,
status VARCHAR(30) NOT NULL DEFAULT 'INVITED',
share_generation INT NOT NULL DEFAULT 1,
revoked_at TIMESTAMPTZ NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE physical_cards (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
generation INT NOT NULL,
package_fingerprint TEXT NOT NULL,
status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
issued_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
revoked_at TIMESTAMPTZ NULL,
UNIQUE(vault_id, generation)
);

CREATE TABLE unlock_requests (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
requested_tier VARCHAR(20) NOT NULL,
trigger_type VARCHAR(30) NOT NULL,
status VARCHAR(40) NOT NULL DEFAULT 'PENDING',
release_delay_expires_at TIMESTAMPTZ NULL,
cancelled_at TIMESTAMPTZ NULL,
completed_at TIMESTAMPTZ NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

CREATE TABLE unlock_approvals (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
unlock_request_id UUID NOT NULL REFERENCES unlock_requests(id) ON DELETE CASCADE,
verifier_id UUID NOT NULL REFERENCES vault_verifiers(id),
decision VARCHAR(20) NOT NULL,
auth_method VARCHAR(30) NOT NULL,
challenge_fingerprint TEXT NOT NULL,
authenticated_at TIMESTAMPTZ NOT NULL,
expires_at TIMESTAMPTZ NOT NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
UNIQUE(unlock_request_id, verifier_id)
);

CREATE TABLE consent_receipts (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
subject_ref UUID NULL,
purpose_code VARCHAR(60) NOT NULL,
consent_text_version VARCHAR(30) NOT NULL,
granted_at TIMESTAMPTZ NOT NULL,
expires_at TIMESTAMPTZ NULL,
revoked_at TIMESTAMPTZ NULL,
evidence JSONB NOT NULL DEFAULT '{}'::jsonb
);

CREATE TABLE anonymous_leads (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
lead_category VARCHAR(50) NOT NULL,
coverage_gap_bracket VARCHAR(50) NULL,
age_bracket VARCHAR(20) NULL,
product_intent VARCHAR(60) NULL,
contact_info_encrypted BYTEA NOT NULL,
assigned_partner_id UUID NULL,
billing_model VARCHAR(30) NOT NULL,
status VARCHAR(30) NOT NULL DEFAULT 'NEW',
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
-- Intentionally no user_id / vault_id foreign key.
);

CREATE TABLE security_events (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NULL,
actor_type VARCHAR(30) NOT NULL,
actor_ref_hash TEXT NULL,
event_type VARCHAR(80) NOT NULL,
event_payload JSONB NOT NULL DEFAULT '{}'::jsonb,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- v1.1.1 additions
CREATE TABLE trusted_devices (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
device_fingerprint_hash TEXT NOT NULL,
status VARCHAR(20) NOT NULL DEFAULT 'ACTIVE',
enrolled_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
revoked_at TIMESTAMPTZ NULL
);

CREATE TABLE unlock_disputes (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
unlock_request_id UUID NOT NULL REFERENCES unlock_requests(id) ON DELETE CASCADE,
reason_code VARCHAR(60) NOT NULL,
status VARCHAR(30) NOT NULL DEFAULT 'OPEN',
opened_at TIMESTAMPTZ NOT NULL DEFAULT NOW(),
resolved_at TIMESTAMPTZ NULL
);

CREATE TABLE document_provenance (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_object_id UUID NOT NULL REFERENCES encrypted_vault_objects(id) ON DELETE CASCADE,
source_type VARCHAR(40) NOT NULL,
verification_status VARCHAR(40) NOT NULL,
content_fingerprint TEXT NOT NULL,
supersedes_document_id UUID NULL,
verified_at TIMESTAMPTZ NULL,
metadata JSONB NOT NULL DEFAULT '{}'::jsonb
);

CREATE TABLE portability_exports (
id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
vault_id UUID NOT NULL REFERENCES vaults(id) ON DELETE CASCADE,
export_format_version INT NOT NULL,
manifest_fingerprint TEXT NOT NULL,
created_at TIMESTAMPTZ NOT NULL DEFAULT NOW()
);

-- logical additions to encrypted_vault_objects:
-- object_revision BIGINT NOT NULL
-- parent_revision BIGINT NULL
-- writer_device_id UUID NULL REFERENCES trusted_devices(id)
```

## 6.1 Data Separation Rules

- Vault ciphertext storage, lead storage, partner CRM data และ marketing analytics ต้องเป็น separate logical domains และ separate access roles

- Lead tables ไม่มี direct user_id/vault_id; correlation หากจำเป็นใช้ one-time token/consent receipt linkage ที่ถูกจำกัดและมี retention

- Contact targets ของ verifiers ต้อง encrypted at rest และเก็บ deterministic hash เฉพาะ use case ที่จำเป็นต่อ lookup/dedup

- Death certificate/evidence files ต้องเป็น encrypted object พร้อม retention policy; URL แบบ public/predictable ห้ามใช้

- Security events เป็น append-only/WORM-capable store และห้ามบันทึก plaintext sensitive payload

# 7. Vault & Unlock State Machine

```text
Vault lifecycle
ACTIVE
-> HEARTBEAT_MISSED
-> OWNER_ESCALATION
-> VERIFIER_CONTACTED
-> ACTIVE (owner checks in)
-> DEATH_VERIFICATION_PENDING
-> DISPUTED
-> SECURITY_FREEZE
-> REJECTED / ACTIVE / DEATH_VERIFICATION_PENDING (explicit resolution)
-> RELEASE_DELAY_48H
-> SECURITY_FREEZE (new fraud/conflict/owner-alive signal)
-> UNLOCKED_TIER2

Temporary delegation lifecycle
ACTIVE
-> TEMP_ACCESS_PENDING
-> TEMP_ACCESS_ACTIVE
-> TEMP_ACCESS_EXPIRED / TEMP_ACCESS_REVOKED

Cancellation path:
DEATH_VERIFICATION_PENDING / RELEASE_DELAY_48H
-> CANCELLED_BY_OWNER
-> ACTIVE

Request terminal states:
COMPLETED | CANCELLED | EXPIRED | REJECTED

SECURITY_FREEZE is non-terminal and MUST NOT auto-complete by timer.
```

## 7.1 State Invariants

- HEARTBEAT_MISSED, OWNER_ESCALATION และ VERIFIER_CONTACTED ไม่ปล่อย Tier 2 recovery material

- RELEASE_DELAY_48H ต้องมี threshold condition satisfied ก่อนเข้า state

- Owner cancellation invalidates pending escrow release for that request

- Completed request ไม่ reuse approvals กับ request ใหม่

- Card revoked หลัง request ถูกสร้างต้องมี policy ชัดเจนว่าจะ invalidate request หรือ require re-verification

- SECURITY_FREEZE blocks Tier 2 release regardless of elapsed RELEASE_DELAY_48H until explicit dispute resolution policy succeeds.

- Temporary delegated access cannot transition directly to UNLOCKED_TIER2 or satisfy post-death threshold by itself.

- Authenticated stale revisions of security-sensitive objects must be rejected or surfaced as conflicts; cryptographic validity alone does not make an old revision current.

# 8. Regulatory Compliance & Security Guardrails (Thailand)

## 8.1 PDPA

- แยก purpose/consent ของ vault operation, sensitive data handling, marketing/lead generation และ partner contact อย่างชัดเจน

- ข้อมูลสุขภาพและความเชื่อทางศาสนา/ปรัชญาเป็นข้อมูลส่วนบุคคลอ่อนไหวตามกรอบ PDPA มาตรา 26 และต้องมีฐานการประมวลผลที่เหมาะสม

- Zero-knowledge encryption ลด exposure แต่ไม่ทำให้ platform พ้นหน้าที่ด้าน PDPA สำหรับ identity, metadata, contact, consent, logs และ processing ที่ platform ยังควบคุม

- จัดทำ Record of Processing Activities, retention schedule, DSAR workflow, incident response และ processor/subprocessor register ก่อน production

- ห้ามนำ vault-derived data ไปทำ behavioral advertising หรือ model training โดย default

## 8.2 Insurance / OIC Regulatory Boundary

| **Regulatory Assumption:** การเรียกค่าบริการว่า CPL, marketing fee หรือ SaaS fee ไม่ได้ยืนยันด้วยตัวมันเองว่ากิจกรรมไม่เข้าข่ายการชี้ช่อง/เสนอขาย/นายหน้าประกันภัย ต้องให้ Thai insurance counsel และ/หรือ คปภ. ยืนยัน operating model ก่อน launch |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

- v1.1.1 product behavior default เป็น educational/contextual card + user-initiated request to contact licensed partner

- Partner license/status ต้องตรวจสอบและมี last_verified_at; UI ห้ามแสดง licensed status ที่หมดอายุโดยไม่เตือน

- Platform ไม่รับ percentage commission จาก premium ใน v1.1.1 เว้นแต่โครงสร้างนิติบุคคล/ใบอนุญาตรองรับและผ่าน legal review

- Agent Sticky Card ต้องไม่ทำให้ผู้ใช้เข้าใจว่า platform รับรองคำแนะนำหรือผลิตภัณฑ์เฉพาะ

## 8.3 Legal Service / Lawyer Directory Boundary

- v1.1.1 ใช้ fixed platform administrative/directory/software fee เป็น baseline; legal fee และ engagement อยู่ระหว่าง client กับ law firm

- ห้าม auto-calculate platform revenue จากเปอร์เซ็นต์ค่าว่าความจนกว่าจะมี legal/ethics opinion ที่เฉพาะเจาะจง

- ระบบแสดง license/firm identity และ conflict disclosures ตามข้อมูลที่ partner ให้และการตรวจสอบที่ทำได้

## 8.4 Will & Living Will Legal Guardrails

- Will templates ต้อง versioned และผ่าน counsel review ก่อนเปิดเป็น production template

- Section 1656 flow ต้องมี signing ceremony checklist เรื่องพยานอย่างน้อย 2 คนพร้อมกัน

- Section 1657 flow ต้องเรียก Handwritten Will Guide และห้ามทำให้ generated printout ถูกเข้าใจว่าเป็น holographic will

- Living Will อ้างอิงสิทธิตาม พ.ร.บ.สุขภาพแห่งชาติ มาตรา 12 และควรเชื่อม/อ้างอิง e-Living Will official resources เมื่อเหมาะสม

- Emergency screen ต้องแยก “self-reported medical info” กับ legally recognized document

## 8.5 Social Security / Benefit Presentation

- ค่าทำศพ/สิทธิประโยชน์ต้องแสดงแบบ eligibility-dependent ตามมาตรา/สถานะและเงื่อนไขปัจจุบัน ไม่ใช้ copy “ทุกคนได้ 50,000 บาท”

- benefit amounts ต้อง config/version ได้และมี source/effective date เพราะสิทธิเปลี่ยนได้

- ถ้าข้อมูลไม่แน่นอนให้พาไป official source/contact แทนการคำนวณเป็นสิทธิแน่นอน

## 8.6 Incapacity / Delegated Access Boundary

Temporary delegated access is a product authorization mechanism and MUST NOT be represented as a court order, statutory authority, power of attorney or medical decision-making authority unless the relevant legal document/status has been separately validated under an approved Thai legal workflow.

# 9. Non-Functional Requirements (NFR)

| **ID**      | **Requirement**             | **Target / Acceptance**                                                                                                                                 |
|-------------|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|
| NFR-SEC-01  | Cryptographic separation    | Tier 1 recovery test cannot decrypt any Tier 2 fixture; automated invariant test in CI                                                                  |
| NFR-SEC-02  | No nonce reuse              | Unique nonce per key/object encryption; crypto test suite detects duplicate nonce in generated fixtures                                                 |
| NFR-SEC-03  | Authentication              | Passkey/WebAuthn preferred; MFA for sensitive recovery/admin operations                                                                                 |
| NFR-SEC-04  | Origin isolation            | vault origin has no third-party scripts/pixels/session replay; CSP blocks unexpected network destinations                                               |
| NFR-SEC-05  | Auditability                | All verifier/recovery/card/key lifecycle events recorded append-only                                                                                    |
| NFR-PRIV-01 | Data minimization           | No raw financial amounts in server analytics; lead payload minimum necessary                                                                            |
| NFR-REL-01  | Durability                  | Encrypted vault/object backups multi-zone; recovery exercises scheduled                                                                                 |
| NFR-REL-02  | RPO/RTO                     | Target RPO \<= 15 min metadata / \<= 24 h immutable backup checkpoint; RTO target \<= 4 h for core access (subject to cost)                             |
| NFR-PERF-01 | Vault UX                    | Local decrypt/render P95 \< 2 s for normal vault; attachment streaming/progressive decrypt for large files                                              |
| NFR-A11Y-01 | Accessibility               | WCAG 2.2 AA target for core flows, especially bereavement/emergency screens                                                                             |
| NFR-I18N-01 | Localization                | Thai-first; English optional; legal/benefit copy versioned by locale                                                                                    |
| NFR-COMP-01 | Retention                   | Documented retention/deletion rules for evidence, leads, logs and partner data                                                                          |
| NFR-SEC-06  | Rollback protection         | Security-sensitive stale revisions cannot silently replace newer committed revisions; automated concurrency/rollback tests in CI                        |
| NFR-ID-01   | Verifier identity assurance | Recovery policy can require configured verifier assurance level independently of authentication method/provider                                         |
| NFR-REL-03  | Portable recovery           | Owner can export a versioned encrypted recovery bundle and validate manifest integrity without plaintext server processing                              |
| NFR-DUR-01  | Service exit continuity     | Documented shutdown/export/key-handling runbook tested before production and reviewed at least annually                                                 |
| NFR-DATA-01 | Legacy freshness            | Configurable freshness windows for verifier/contact/policy/recovery-card metadata; stale state visible to owner without exposing plaintext to analytics |

# 10. Analytics, Observability & Privacy-Safe Telemetry

- Operational telemetry may capture latency, error code, browser family, crypto_version and object count bucket but never plaintext values/content

- Product analytics must use allow-listed events and payload schemas; unknown fields rejected server-side

- Coverage gap raw values remain local; coarse bucket only after documented need and consent/legal basis

- Security events separated from growth analytics and retained according to security/legal policy

- Partner/lead conversion measurement uses lead_id and partner status; avoid joining back to vault content

- Freshness telemetry may record coarse status such as CURRENT/STALE/UNKNOWN and object-category counts, but must not send policy numbers, balances, document content or verifier contact values.

- Device/revision telemetry may record opaque device ID, revision sequence and conflict/error codes; plaintext semantic diffs stay client-side.

# 11. Monetization & Packaging - v1.1.1

| **Revenue Stream**      | **Model**                                               | **Guardrail**                                                                                                |
|-------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| Lifetime Vault Pass     | 399 THB one-time, VAT-inclusive assumption (hypothesis; ADR-007 Proposed) | vault features, initial physical card (Phase 2) and heartbeat over LINE/Email/push (Phase 2); SMS/IVR excluded; provider costs and quotas apply; no recurring consumer fee does not mean no recurring operating cost; lifetime scope, sunset policy and funded reserve are open (ADR-007 OD-2) |
| Agent SaaS              | 490-990 THB/month                                       | relationship/branding/invite tools; no plaintext access                                                      |
| Contextual Ads          | fixed placement/directory/marketing arrangement         | matching local; no third-party behavioral tracking in vault                                                  |
| Insurance Leads         | Fixed CPL / marketing fee subject to legal confirmation | explicit user request + licensed partner + separate consent                                                  |
| Legal/Funeral Directory | fixed admin/directory/software fee baseline             | service contract/fee between user and provider where applicable                                              |
| Corporate HR            | per employee / annual enterprise plan                   | strict separation of employer data vs personal vault                                                         |

# 12. Delivery Scope & Phasing

## Phase 0 - Legal/Security Design Freeze

- Thai counsel review of will templates, Living Will copy, insurance lead model and legal directory model

- ADR: key hierarchy, crypto library, recovery threshold, escrow release, web threat model

- Abuse-case workshop: owner alive, family dispute, malicious verifier, partner compromise, lost card, shared device, death certificate fraud

- Data map / PDPA RoPA / retention model

- Owner/device recovery threat model + tested owner-held recovery path

- Service-sunset / portable recovery format decision and documented minimum export contract

- Verifier identity-assurance policy baseline and provider abstraction

## Phase 1 - Core Vault MVP

- Account + Passkey/MFA

- Tier 1/Tier 2 encrypted objects with separate keys

- 5-item Free Tier asset/liability/insurance mapping

- Will guide v1 + encrypted attachments

- Basic verifier invite and recovery kit without automated dead-man escalation

- Local coverage gap calculation

- Trusted-device enrollment/revocation + security-sensitive revision lineage

- Basic document provenance metadata and freshness indicators

## Phase 2 - Recovery & Bereavement

- Physical card lifecycle/revocation

- Request-scoped 2-of-3 recovery

- 48-hour release delay + multi-channel owner cancellation

- Stage-based bereavement checklist

- OIC My Legacy guided handoff

- Benefit source/version configuration

- DISPUTED / SECURITY_FREEZE recovery workflow and operational runbook

- Portable encrypted export + vault closure / cryptographic-erasure workflow

- Temporary delegated-access pilot only after legal/security policy review

## Phase 3 - Monetization & B2B2C

- Privacy-Limited Quote + consent receipts + lead isolation

- Agent portal/sticky card/license verification workflow

- Corporate HR benefit catalog and beneficiary planning statuses

- Partner directory/billing and conversion feedback loop

# 13. Architecture / Security Acceptance Criteria

| **ID** | **Acceptance Criterion**                                                                                                                                    |
|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| AC-01  | A Tier 1 recovery fixture cannot derive K_TIER2 or decrypt Tier 2 ciphertext under any supported client path.                                               |
| AC-02  | Every encrypted object contains a unique nonce for a given key and has authenticated AAD binding tier/object/schema identity.                               |
| AC-03  | No unlock approval persists as a reusable verifier boolean; all approvals are request-scoped and expiring.                                                  |
| AC-04  | Inactivity state transitions never release Tier 2 key material without threshold/death-verification policy.                                                 |
| AC-05  | Revoking/reissuing a physical card invalidates prior generation and offers/share-rotation flow when compromise is suspected.                                |
| AC-06  | Agent/HR/partner roles cannot query decrypted vault content via any server API.                                                                             |
| AC-07  | vault origin loads zero third-party tracking scripts and CSP tests fail build/deploy on unauthorized endpoints.                                             |
| AC-08  | Lead creation is impossible without a versioned consent receipt and minimum-necessary payload.                                                              |
| AC-09  | Section 1657 export clearly states generated file is a guide, not the final handwritten will.                                                               |
| AC-10  | Section 1656 signing guide explicitly requires at least two witnesses present together at signature time.                                                   |
| AC-11  | Benefit amounts and regulatory references are source/versioned and can be updated without redeploying vault crypto.                                         |
| AC-12  | Recovery/support runbook demonstrates no support-admin bypass that yields plaintext or MVK.                                                                 |
| AC-13  | Platform support cannot reset an owner account in a way that yields plaintext vault content or Tier keys without owner-held recovery material.              |
| AC-14  | Revoked devices cannot decrypt newly rotated/re-wrapped key material, and new-device enrollment generates a security notification/event.                    |
| AC-15  | A request in DISPUTED / SECURITY_FREEZE cannot release Tier 2 material or auto-complete when RELEASE_DELAY_48H expires.                                     |
| AC-16  | Security-sensitive stale revisions cannot silently overwrite a newer committed revision; conflict/rollback tests cover offline reconnect and replay.        |
| AC-17  | A valid portable recovery export contains a versioned integrity-checkable manifest and can be parsed/validated without access to production plaintext APIs. |
| AC-18  | Tier 2 recovery policy can enforce verifier identity-assurance requirements independently of authentication provider/method.                                |
| AC-19  | Temporary delegated access is time-bounded/revocable and cannot derive K_TIER2 or satisfy post-death unlock by itself.                                      |
| AC-20  | Vault closure revokes cards/verifiers and destroys/revokes applicable wrapped recovery material before ciphertext deletion according to retention policy.   |

# 14. Open Decisions / ADR Backlog

| **ADR** | **Decision Required**                                            | **Default v1.1.1 Position**                                                                                                    |
|---------|------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| ADR-001 | AES-256-GCM vs XChaCha20-Poly1305                                | Either acceptable via vetted library; choose one canonical format + versioning                                                 |
| ADR-002 | Owner account/device recovery construction                       | Owner-held recovery material + trusted-device lifecycle + local re-wrap; no support-admin plaintext/key reset                  |
| ADR-003 | Exact Shamir topology / escrow implementation                    | 2-of-3 with platform + 2 owner-selected trusted persons; agent not default                                                     |
| ADR-004 | Death certificate verification                                   | Manual/provider/official verification route; no automatic OCR-only approval                                                    |
| ADR-005 | Tier 1 authentication policy                                     | Card + designated verifier/auth; must not disclose Tier 2 material                                                             |
| ADR-006 | Web app vs native companion for higher-assurance vault           | Web MVP with hardened origin; evaluate signed native/PWA companion for stronger assurance                                      |
| ADR-007 | Pricing and notification cost envelope                           | Single Lifetime Vault Pass (399 THB); heartbeat on free channels only; no recurring consumer entitlement                       |
| ADR-008 | OIC integration                                                  | Guided handoff until official API/agreement confirmed                                                                          |
| ADR-009 | Partner licensing verification source/cadence                    | Use official verification where available; record last_verified_at                                                             |
| ADR-010 | Sensitive data lawful bases and explicit consent UX              | Finalize with DPO/legal counsel and RoPA                                                                                       |
| ADR-011 | Verifier identity proofing provider / assurance mapping          | Provider-agnostic IAL policy; evaluate ThaiD/regulated identity routes without making any provider a Tier 2 cryptographic root |
| ADR-012 | Portable recovery bundle format and service-sunset escrow policy | Documented versioned encrypted bundle + annual service-exit exercise                                                           |
| ADR-013 | Multi-device concurrency / revision protocol                     | Authenticated monotonic revision lineage; reject/surface stale security-sensitive writes                                       |
| ADR-014 | Temporary incapacity / delegated-access legal model              | Separate from post-death flow; limited pilot only after Thai legal/security review                                             |
| ADR-015 | Document provenance verification sources                         | Start SELF_DECLARED/USER_UPLOADED; add provider/official verification only with defined evidence and freshness rules           |

# 15. Regulatory & Reference Sources Used for v1.1.1

**Civil and Commercial Code, Section 1656:** พินัยกรรมแบบธรรมดา: ต้องทำเป็นหนังสือ ลงวันเดือนปี และลงลายมือชื่อต่อหน้าพยานอย่างน้อยสองคนพร้อมกัน; พยานลงชื่อรับรองในขณะนั้น [https://www.thailawonline.com/th/thai-civil-code/section-1656/](https://www.thailawonline.com/th/thai-civil-code/section-1656/)

**Civil and Commercial Code, Section 1657:** พินัยกรรมเขียนเองทั้งฉบับ: ผู้ทำเขียนข้อความทั้งหมด วัน เดือน ปี และลายมือชื่อด้วยมือตนเอง [https://deka.in.th/laws/ccc/1657](https://deka.in.th/laws/ccc/1657)

**National Health Commission Office - Section 12:** Living Will / หนังสือแสดงเจตนาไม่ประสงค์รับบริการที่เป็นไปเพียงเพื่อยืดการตายในวาระสุดท้าย [https://www.nationalhealth.or.th/public/page/statute12](https://www.nationalhealth.or.th/public/page/statute12)

**National Health Commission Office - e-Living Will:** Official e-Living Will information [https://www.nationalhealth.or.th/elivingwill](https://www.nationalhealth.or.th/elivingwill)

**OIC Center of InsurTech - OIC Connect:** My Legacy (สืบค้นกรมธรรม์โดยทายาท), My Policy, My Coverage และบริการยืนยันตัวตนผ่าน ThaiD [https://cit.oic.or.th/en/oicconnect](https://cit.oic.or.th/en/oicconnect)

**Ministry of Labour:** ตัวอย่างสิทธิกรณีเสียชีวิตของผู้ประกันตน ม.33/39 และค่าทำศพ 50,000 บาทตามเงื่อนไขที่เกี่ยวข้อง [https://www.mol.go.th/](https://www.mol.go.th/)

**Personal Data Protection Act B.E. 2562:** มาตรา 26 และข้อกำหนดเกี่ยวกับ sensitive personal data เช่น สุขภาพและความเชื่อทางศาสนา/ปรัชญา *Official Thai statute / PDPC materials; verify current notices before production*

| **Source Control:** Regulatory content can change. Production copy, benefit amounts, partner licensing rules and legal templates must carry source/effective date and be reviewed at release time. |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|

# Appendix A - v1.0 to v1.1.1 Requirement Mapping

| **v1.0 Item**                                             | **v1.1.1 Disposition**                                                           |
|-----------------------------------------------------------|----------------------------------------------------------------------------------|
| FR-1.1 Pro/Lifetime includes ongoing multi-channel switch | Split into Lifetime Vault Pass + optional Continuity Service                     |
| FR-1.3 1657 generator                                     | Renamed/limited to Handwritten Will Guide with strong non-final-document warning |
| FR-2.3 Anonymous Quote                                    | Renamed Privacy-Limited Quote; versioned consent + lead isolation                |
| FR-3.1 Agent can hold backup share                        | Removed as default; owner must explicitly select; independent backup recommended |
| FR-3.2 beneficiary in one place                           | Converted to Beneficiary Planning & Status Hub with provider-confirmation states |
| FR-4.1 inactivity chain                                   | Converted to escalation state machine; cannot unlock Tier 2 alone                |
| FR-4.2 Tier 1/Tier 2 under one MVK                        | Replaced with independent K_TIER1 and K_TIER2                                    |
| FR-4.2 Time-Lock 48h                                      | Renamed release delay unless cryptographic timelock implemented                  |
| encrypted_vaults.encryption_iv                            | Replaced by per-object nonce in encrypted_vault_objects                          |
| vault_verifiers.has_approved_unlock                       | Removed; replaced by unlock_approvals per request                                |
| server_key_share + QR raw share model                     | Revised to encrypted/revocable recovery package and explicit share lifecycle     |
| Post-funeral insurance discovery                          | Added OIC My Legacy guided handoff                                               |

# Appendix B - Security Invariants (Normative)

- MUST NOT: server or partner possess sufficient default material to decrypt a live vault without owner/recovery policy.

- MUST NOT: Tier 1 unlock expose any key/share capable of Tier 2 decryption.

- MUST NOT: reuse AEAD nonce with the same key.

- MUST NOT: store raw Shamir share as a permanently valid bearer token in QR/NFC.

- MUST NOT: reuse verifier approval across unlock requests.

- MUST NOT: allow inactivity alone to complete Tier 2 unlock.

- MUST NOT: load third-party tracking code in decrypted vault origin.

- MUST: support verifier/card/share rotation and revocation.

- MUST: bind ciphertext to object identity/tier/schema with authenticated metadata.

- MUST: capture consent evidence separately for marketing/lead purposes.

- MUST: maintain append-only security events without sensitive plaintext.

- MUST: present legal/benefit information as versioned guidance with source/effective date.

# Appendix C - v1.1 to v1.1.1 Additions

| v1.1 Gap                                                       | v1.1.1 Addition                                                  |
|----------------------------------------------------------------|------------------------------------------------------------------|
| Owner session recovery remained ADR-only                       | FR-1.6 owner/device recovery + trusted-device journey + AC-13/14 |
| No explicit disputed unlock state                              | FR-4.6 + DISPUTED/SECURITY_FREEZE state/invariants + AC-15       |
| Authentication not separated from identity proofing            | FR-1.7 + identity-assurance architecture/NFR + ADR-011           |
| No service-sunset / portable independent recovery contract     | FR-5.3 + portable recovery architecture/NFR + ADR-012            |
| No security-sensitive multi-device revision semantics          | FR-5.5 + rollback architecture/NFR + ADR-013                     |
| Incapacity, vault closure and document provenance not explicit | FR-4.7 / FR-5.4 / FR-5.6 as phased requirements                  |

**END OF PRD v1.1.1**
