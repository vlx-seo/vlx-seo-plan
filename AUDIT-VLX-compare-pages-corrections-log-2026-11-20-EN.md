# VLX Compare Pages → Blog: Findings & Corrections Log

**Date:** 2026-11-20 · **Author:** lceballos-seo
**Scope:** `vlx.ai/compare/vlx-vs-goaudits/` and `vlx.ai/compare/vlx-vs-safetyculture/`, rewritten as blog posts.
**Method:** every claim was checked **live today** against each vendor's own website and help center. Nothing was taken from memory or old notes. Research agents covered every row; every fact used in the posts was then re-verified by hand, and anything that couldn't be confirmed was removed.
**Deliverables:**
- `CONTENT-VLX-SafetyCulture-Mitti-alternative-2026-11-20-EN.md`: corrected post → `/blog/safetyculture-alternative/`
- `CONTENT-VLX-GoAudits-alternative-2026-11-20-EN.md`: corrected post → `/blog/goaudits-alternative/`
- This log

---

## 1. Current state of the compare pages

| Item | Finding |
|---|---|
| HTTP / indexing | Both return 200 with `noindex, nofollow` (soft-unpublished on 2026-06-26, commit 89057bd) |
| Sitemap / internal links | Not in the sitemap; not linked from the homepage |
| Source vs live | Live HTML matches `origin/master` `src/app/(frontend)/compare/*/page.tsx`. Last content edit: June 2026 |
| Breadcrumb | BreadcrumbList JSON-LD points to `/compare/`, which returns **404** |
| robots.txt | The code comment says the pages are blocked in robots.txt. **They are not anymore.** `noindex` is the only protection |
| Search demand (Ranki, US) | `safetyculture pricing` 70 (KD 0) · `mitti safetyculture` 390 (new since Jul 2026, peak 1,900 in Sep) · `iauditor alternative` 30 (KD 0) · `safetyculture alternatives` 20 (KD 0) · `goaudits pricing` 10 · `goaudits alternative` no data |
| GSC | Not available: Search Console isn't connected in Ranki |

---

## 2. Critical finding: SafetyCulture is now "Mitti"

- On **11 Aug 2026** SafetyCulture rebranded as **Mitti (by SafetyCulture)**.
- `safetyculture.com`, `/pricing` and `help.safetyculture.com` all **301 → mitti.com**. The help center is now help.mitti.com.
- Source: https://mitti.com/media-releases/safetyculture-becomes-mitti-unveiling-a-platform-built-for-the-future-of-frontline-work
- **Correction:** "SafetyCulture (formerly iAuditor)" → "**Mitti (formerly SafetyCulture / iAuditor)**". Keep "SafetyCulture" in the title, because that is still what people search for.

---

## 3. Corrections to VLX's own claims (both pages)

| # | Old claim | Live evidence (vlx.ai) | Correction applied |
|---|---|---|---|
| V1 | KYPiT validates "image authenticity, user identity, **device fingerprinting**, location, **behavioral analysis**" (5 table rows) | 5 official categories: **Image Validation, User Identity Validation, Domain Analytics, Activity Analytics, IP Analytics** (/digital-inspections-software/app/kypit/, /features/, /faqs/). "Device fingerprinting" appears nowhere on the site. | Rows rewritten to the 5 real categories |
| V2 | "AI-Powered **Fraud Detection**" | /faqs/: "Does KYPiT prevent fraud? **No.** KYPiT does not block submissions…". Pricing: "KYPiT data validation". | Now "data validation"; KYPiT **flags**, it doesn't block |
| V3 | "**Every** VLX customer gets a dedicated team member — **not just enterprise**". "A VLX team member will help you set up your templates" (trial CTA) | /faqs/: "Pro and Pro+ are **self-serve**"; "**Enterprise** customers get a dedicated CSM". Pricing: "Dedicated relationship manager" on Enterprise. | **Removed.** Onboarding, CSM and template setup are now labelled Enterprise |
| V4 | Custom buttons trigger "status changes, notifications, **API calls**" | /help/action-buttons/: lock the page, change status or custom status, email people. No API calls. Name: **Action Buttons**, Pro+. | "API calls" removed; renamed Action Buttons |
| V5 | "Scheduled inspections" ✓ | Only "Assignment & scheduling… with target completion dates". Recurring scheduling exists only in VLX Home (Early Access). | Row removed |
| V6 | Every VLX feature shown as ✓, with no plan | /features/ lists a minimum tier per feature. KYPiT, SSO, Word, Excel, REST API, webhooks, cloud sync and BI are **Enterprise**. Custom statuses, locking, Action Buttons, sharing and Zapier are **Pro+**. | Every row now shows the plan |
| V7 | Pricing: none shown | Pro **$29** annual / $35 monthly. Pro+ **$47** annual / $55 monthly. Enterprise custom. 15-day trial, no credit card. | Pricing table added |
| V8 | "SOC 2 Type 2 compliance" | /about-us/security/: "SOC 2 Type II **Attested**" | ✅ Kept ("attested", never "certified") |
| V9 | "Start a free trial… No credit card required" | /pricing/: "15-day free trial on Pro or Pro+. No credit card required." | ✅ Kept; length (15 days) and plans added |
| V10 | Omitted features | Live and marketed: AI Template Builder, AI Scribe, AI Walkthroughs, 1,000+ public templates, Virtual Inspections, COUNTiT/Instacount, image metadata watermark, custom approval workflows, cloud storage sync, BI integration | AI Suite and the template library added to both posts |

### 3a. Inconsistencies inside vlx.ai: resolved

**Tie-breaker used:** the live feature comparison table on /pricing/ ("Show the feature comparison"; source `src/app/(frontend)/pricing/PricingContent.tsx`). It matches /features/ and is the page customers buy from.

| Topic | Conflict found | Resolution (per /pricing/ table) | Fixed in code |
|---|---|---|---|
| KYPiT categories | KYPiT page ("What sets it apart" + JSON-LD), /product/, navbar menu and the fraud-detection campaign landing used the old "image, identity, device, location, behavior" list | Image Validation, User Identity Validation, Domain Analytics, Activity Analytics, IP Analytics | ✓ |
| KYPiT tier | "Pro+ and Enterprise" on /help/kypit/ and the campaign landing | **Enterprise only** | ✓ |
| AI Template Builder / AI Walkthroughs tier | "Pro+ and above" on the homepage FAQ + JSON-LD, /faqs/, features accordion, AI roadmap teaser | **Every plan** (Pro ✓) | ✓ |
| AI Scribe custom skills tier | "custom skills on Pro+" | **Every plan** (Pro ✓) | ✓ |
| REST API tier | Enterprise (/features/) vs "API access" in Pro+ (/pricing/ card) | Not a real conflict: **Pro+ on request, included on Enterprise** | n/a (posts use this wording) |
| Word reports tier | Enterprise vs "Pro+ … upon request" | Not a real conflict: **Pro+ on request, included on Enterprise** | n/a (posts use this wording) |
| KYPiT positioning | "fraud detection" vs FAQ "does not prevent fraud" | Not a real conflict: KYPiT *detects and flags*; it doesn't *block*. The posts say "data validation" + "flags". | n/a |
| KYPiT name expansion | "Know Your Product" vs "Know Your Product, Process, Provenance" | Not used in the posts; left unchanged (brand decision) | — |

**Code fix:** branch `content/fix-kypit-ai-tier-claims` (from `origin/dev`), commit `1eea005`. 11 files, copy-only. Local worktree: `Git/vlx-compare-claims-wt`.

---

## 4. GoAudits: corrections

Pricing (live, goaudits.com/pricing, per user/month):
- **Starter:** $10 annual / $12 monthly.
- **Enterprise:** $30 annual / $35 monthly.
- **SSO:** "Contact for pricing" add-on.
- **Trial:** 14 days, no card.
- **Guarantee:** 60-day money-back.

| Row | Old page | Live finding | Verdict | Source |
|---|---|---|---|---|
| SSO (Entra ID) | ✗ | SAML 2.0 with **Microsoft Azure AD**/Okta; paid Enterprise add-on | **Wrong** ✔ | support.goaudits.com/en/articles/13176270 |
| Dedicated CSM | ✗ | "Dedicated Account Manager — YES - for annual plans over 3 users" | **Wrong** ✔ | goaudits.com/pricing/ |
| Template setup assistance | ✗ | "Free checklist setup from our team!" | **Wrong** ✔ | goaudits.com/pricing/ |
| Custom onboarding | ✗ | Free onboarding program, booked through a demo | **Wrong** | goaudits.com/free-trial/ |
| Location verification | ✗ | GPS **geofencing** blocks starting audits outside the boundary (Enterprise) | **Wrong** ✔ | support…/9691522 |
| Excel exports | "Single view" | "Export - Details" (low-level) + "Export - Summary" (high-level) | **Wrong** ✔ | support…/4741646 |
| Custom Word reports | ✗ | "Reports in Word/Excel" on Enterprise; no custom .docx templates documented | **Partial** ✔ | pricing |
| Daily activity reports | ✗ | Daily/weekly digest emails | **Partial** ✔ | goaudits.com/workflows/ |
| Inspection locking | ✗ | On reopen, location, date and auditor "are locked by design"; Audit Date Lock | **Partial** ✔ | support…/6542271 |
| Third-party sharing | ✗ | External users "don't need a dedicated login or paid license"; they get an email link to their action | **Partial** ✔ | goaudits.com/workflows/ |
| Roles / hierarchy | "Basic" / "Limited" | Custom roles and "Roles & Hierarchy" on Enterprise | Understated | support…/5464364 ✔ |
| Repeatable sections | "Limited" | None; only cloning | Changed to ✗ | support…/4298758 ✔ |
| Video/audio | "Photos only" | Correct; there is also a document-upload add-on | OK + nuance | support…/15677195 ✔ |
| AI | ✗ | **No AI features found** anywhere | ✅ Correct (absence finding) | site + help center |
| Calculated fields, barcode, free-form, custom statuses, custom buttons, field ID mapping, behavioral/device | ✗ | Not found | ✅ Correct | help center searched (absence finding) |
| Webhooks / Zapier | "API only" | Read/export REST API (Enterprise); no webhooks or Zapier found | ✅ Correct | support…/4738393 ✔ |
| Narrative "more affordable for small teams" | — | Fair for Starter. But many GoAudits ✓ items are on the $30 Enterprise plan, roughly VLX Pro ($29). | Kept, with a pricing caveat | pricing |
| Security | — | The agent reported "ISO 27001 certified", but it **could not be found** on goaudits.com when re-checked | **Removed** from the post | — |

✔ = re-verified by hand on 2026-11-20. "Absence finding" = nothing found after searching the whole help center; the posts phrase these as "we found no…" or plain ✗.

---

## 5. SafetyCulture → Mitti: corrections

Pricing (live, mitti.com/pricing):
- **Free:** $0, up to 10 users, 5 active templates, 300 AI credits/seat/mo.
- **Premium:** **$24/seat/mo annual**, $29 monthly.
- **Enterprise:** custom.
- **AI:** "AI included in every plan".
- **Trial:** the Premium trial **collects a credit card**. Mitti's own trial length is inconsistent: 30 days in its schema, 14 in a modal.

| Row | Old page | Live finding | Verdict | Source |
|---|---|---|---|---|
| Name | "SafetyCulture (formerly iAuditor)" | **Mitti (by SafetyCulture)** since 11 Aug 2026 | **Wrong** ✔ | press release, 301s |
| AI-generated templates | ✗ | "The AI Assistant generates a structured template based on your input" | **Wrong** ✔. A former VLX "advantage" is gone. | help.mitti.com/000087 |
| Custom Word reports | ✗ | "Word export is only available to organizations on Premium Plan or Enterprise Plan" | **Wrong** ✔. The difference is VLX's *custom* templates + Field IDs. | help.mitti.com/000055 |
| Calculated fields | "Limited" | Calculation questions with functions; numeric/slider inputs only | Mostly ✓ | help.mitti.com/002991 ✔ |
| No-code triggers | ✗ | Logic triggers, Zapier, Power Automate, integrations builder (pricing page), scheduled Agents. No "custom buttons". | **Partial** | help.mitti.com/000031, mitti.com/integrations ✔ |
| CSM / onboarding | "Enterprise only" | "Onboarding services and training: Contact sales" on **Premium and Enterprise** | **Partial** ✔ | mitti.com/pricing |
| Location verification | ✗ | GPS per response, "ensuring inspections happen at the correct location" | **Partial** ✔ | help.mitti.com/002230 |
| Video & audio | ✓ | Video up to 3 min ✔. No audio among inspection media ✔ (voice notes only in AI issue creation). | **Partial** | help.mitti.com/001645 |
| Signatures with GPS | ✓ | Signature response exists; GPS is not tied to the signature | **Partial** | help.mitti.com/002056 ✔ |
| Free-form (no template) | ✗ | Inspections need a template; Issues can be reported without one | **Partial** | help.mitti.com/000087, /000103 ✔ |
| Inspection locking | ✗ | "Once an inspection is approved, you can still edit it" | ✅ Correct ✔ | help.mitti.com/000009 |
| Barcode | "Basic" | QR, EAN, UPC, Code 128/39, ITF scanned into text/number fields; no parsing | ✅ Correct ✔ | help.mitti.com/001713 |
| API, webhooks, Zapier, SSO (Azure), ERP/CRM | ✓ | Correct, but **Premium+ only** (SSO Azure ✔) | OK + plan added | help.mitti.com/000017, /000077 |
| AI/fraud validation | ✗ | No validation or anti-fraud features in the AI feature list (updated 3 Nov 2026) | ✅ Correct | help.mitti.com/003943 ✔ |
| Template library, Sensors/IoT | SC strengths | "Thousands of free templates" ✔; Sensors & IoT plus telematics still offered | ✅ Correct | help.mitti.com/000087, mitti.com |
| Template setup / white-glove | ✗ | Not verifiable; onboarding services and a partner network are sold | Removed | mitti.com/pricing, /integrations |
| Omitted 2025–26 features | — | AI Assistant, Agents, AI issue creation, AI report summaries, Training (EdApp), Assets/telematics, Contractor Management, Mitti Insurance | Added to "Where Mitti is stronger" | help.mitti.com/003943, mitti.com |

---

## 6. VLX differentiators that remain true (used in both posts)

1. **KYPiT data validation** across 5 categories (Enterprise). Neither competitor offers an equivalent.
2. **Custom Word (.docx) templates with Field ID mapping** (Enterprise). Competitors only export standard layouts to Word.
3. **Free-form inspections** in a timeline (Pro).
4. **Barcode output parsing** into separate fields (Pro).
5. **Page-level locking via Action Buttons** + custom inspection statuses (Pro+).
6. **Third-party sharing with 3 roles** (Pro+).
7. **No-credit-card trial** (vs Mitti, which collects a card).
8. **AI Suite vs GoAudits**, which has no AI. Against Mitti, AI is now table stakes, not a differentiator.

---

## 7. Publishing

- `/blog/safetyculture-alternative/` and `/blog/goaudits-alternative/` go into Payload as posts (title, meta and keyword are in each draft's metadata block).
- On publish, add `308 /compare/vlx-vs-safetyculture/ → /blog/safetyculture-alternative/` and `308 /compare/vlx-vs-goaudits/ → /blog/goaudits-alternative/`, and remove the two compare routes (that also removes the broken `/compare/` breadcrumb).
- Cross-link both posts with each other and with the Spectora alternative post.
