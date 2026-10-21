# Fact-Check Log

Last checked: **2026-10-21**. This covers factual claims in PRD v1.1.1 and the repo docs.
It is engineering due diligence, **not legal advice**. Thai counsel still needs to confirm
every legal item before production. Re-check at each release (PRD §15).

Legend: ✅ confirmed · ⚠️ confirmed with corrections or additions · ❓ couldn't verify · 🆕 new fact the PRD doesn't cover

## Legal & regulatory

| # | Claim | Result | Notes / action | Source |
|---|---|---|---|---|
| F-01 | CCC §1656: written, dated, signed before ≥ 2 witnesses present together, who certify at that time | ✅ ⚠️ | Also: alterations are invalid unless made in the same form (para 2). Added to CONTEXT and REVIEW-NOTES R-17 | [thailawonline §1656](https://www.thailawonline.com/th/thai-civil-code/section-1656/) |
| F-02 | CCC §1657: entire text, date and signature handwritten | ✅ ⚠️ | Also: alterations must be handwritten and signed. §9 para 2 is excluded, so no fingerprint or mark in place of a signature | [thailawonline §1657](https://www.thailawonline.com/th/thai-civil-code/section-1657/) |
| F-03 | (not in PRD) Witness/writer and their spouses can't benefit under the will | 🆕 | CCC §1653. Belongs in the signing checklist | [thailawonline §1653](https://www.thailawonline.com/th/thai-civil-code/section-1653/) |
| F-04 | (not in PRD) Witness disqualifications | 🆕 | CCC §1670: minors, unsound mind or quasi-incompetent, deaf, mute or blind in both eyes | [thailawonline §1670](https://www.thailawonline.com/th/thai-civil-code/section-1670/) |
| F-05 | (not in PRD) Minimum testamentary age | 🆕 | CCC §25: 15 years old | [thailawonline §25](https://www.thailawonline.com/th/thai-civil-code/section-25/) |
| F-06 | NHA §12 Living Will | ✅ ⚠️ | Covers refusing services that only prolong dying at the end of life **or** prolong suffering. Procedures are in the Ministerial Regulation B.E. 2553 (in force 2011), which allows appointing a proxy | [NHCO §12](https://www.nationalhealth.or.th/public/page/statute12), [Ministerial Regulation (EN)](https://en.nationalhealth.or.th/wp-content/uploads/2017/11/Final_Ministerial_Regulation-.pdf) |
| F-07 | PDPA §26: health and religious/philosophical beliefs are sensitive data | ✅ | The full list is longer (see CONTEXT §5). Explicit consent is the default basis. Based on secondary summaries, so verify the official text | [Tilleke](https://www.tilleke.com/insights/thailand-operationalising-pdpa-lawful-basis-sensitive-personal-data-and-data-processing-safeguards/26/), [Securiti](https://securiti.ai/pt-br/blog/consent-requirements-under-thailands-data-protection-framework) |
| F-08 | OIC Connect offers *My Legacy* for heirs, with ThaiD | ✅ | Launched as part of the OIC Connect super app (news dated June 2026). No public API is documented, which supports the guided-handoff default (ADR-008) | [OIC CIT](https://cit.oic.or.th/en/oicconnect), [share2trade](https://www.share2trade.com/news/56634/www.dittothailand.com) |
| F-09 | Fee labels don't by themselves settle the insurance licensing question | ✅ 🆕 | **New: the OIC guidelines for unlicensed persons on digital media (B.E. 2569, in force 24 Jul 2026)** reserve for licensees: soliciting or facilitating contracts, personalized advice, links that facilitate contract formation, and performance-based compensation tied to policies or premiums. They also require disclosure. Penalty up to 6 months' prison or 50,000 THB. See R-16 | [Tilleke (Aug 2026)](https://www.tilleke.com/insights/thailand-identifies-online-triggers-of-insurance-licensing-requirements/) |
| F-10 | Social Security funeral grant 50,000 THB "subject to conditions" | ✅ ⚠️ | §33/§39: non-work-related death, ≥ 1 month of contributions in the 6 months before death. Paid to the funeral arranger. Death compensation is 2 or 6 months' average wage (36–119 or ≥ 120 contribution months). §40 depends on the option, and sources disagree. Work-related death falls under the Workmen's Compensation Fund (50,000 THB since Jul 2021). See R-18 | [SSO](https://www.sso.go.th/wpr/main/privilege/%E0%B8%81%E0%B8%AD%E0%B8%87%E0%B8%97%E0%B8%B8%E0%B8%99%E0%B8%9B%E0%B8%A3%E0%B8%B0%E0%B8%81%E0%B8%B1%E0%B8%99%E0%B8%AA%E0%B8%B1%E0%B8%87%E0%B8%84%E0%B8%A1_detail_detail_1_125_693/231_231), [Thai PBS](https://www.thaipbs.or.th/news/content/342090), [Matichon (WCF)](https://www.matichon.co.th/local/news_2828289), [Thansettakij (§40)](https://www.thansettakij.com/health-wellness/622395) |
| F-11 | (implied) Generated or e-signed documents can't serve as a will | ❓ | I couldn't access the text of the Royal Decree B.E. 2549 on transactions excluded from the Electronic Transactions Act. Secondary sources say e-wills aren't recognized. Counsel to confirm (R-19) | [ETA (ETDA)](https://www.etda.or.th/getattachment/f2c20e25-bcd4-4920-a7b0-b6c350833c43/Electronic-Transactions-Act-B-E-2544-(Amendment).aspx) |

## Technical

| # | Claim | Result | Notes / action | Source |
|---|---|---|---|---|
| T-01 | AES-GCM with random 96-bit IVs has a per-key invocation limit | ✅ | NIST SP 800-38D: ≤ 2³² invocations with random IVs. That supports XChaCha20-Poly1305 (ADR-001). SP 800-38D is under revision | [AWS Security Blog](https://aws.amazon.com/blogs/security/how-aws-kms-and-aws-encryption-sdk-overcome-symmetric-encryption-bounds), [NIST SP 800-38D r1](https://csrc.nist.gov/pubs/sp/800/38/d/r1/2prd) |
| T-02 | RustCrypto AEAD crates are "audited" | ⚠️ | One limited audit (NCC Group, Feb 2020, about 5 person-days, no significant findings). Wording in ADR-016 corrected | [NCC Group](https://www.nccgroup.com/research/public-report-rustcrypto-aesgcm-and-chacha20pluspoly1305-implementation-review/), [docs.rs aes-gcm](https://docs.rs/aes-gcm/0.10.1) |
| T-03 | Trusted Types are usable "where supported" | ✅ | Now Baseline: Chrome/Edge 83+, Safari 26, Firefox 148 (Feb 2026). Enforcement in `web-vault` is practical across engines | [web.dev Baseline Feb 2026](https://web.dev/blog/baseline-digest-feb-2026?hl=vi), [URIports](https://www.uriports.com/blog/csp-trusted-types/) |
| T-04 | WebAuthn PRF can back device wrapping keys | ⚠️ | Support is uneven (iOS 18+, Android, Chromium, Firefox 148 on Windows Hello), and some password-manager extensions are non-compliant. The fallback is mandatory (ADR-002) | [Corbado](https://www.corbado.com/blog/passkeys-prf-webauthn), [1Password community](https://www.1password.community/developers-69/browser-extension-webauthn-prf-extension-not-spec-compliant-24672) |
| T-05 | ThaiD is the government digital ID app (DOPA) | ✅ | — | [share2trade](https://www.share2trade.com/news/56634/www.dittothailand.com) |

## PRD internal consistency

| # | Finding | Action |
|---|---|---|
| C-01 | AC-12 still says "MVK", which v1.1.1 removed | R-05 |
| C-02 | The `anonymous_leads` table name conflicts with "Privacy-Limited Quote" | R-05, D-07 |
| C-03 | §7 merges the vault heartbeat and unlock-request lifecycles | R-04 |
