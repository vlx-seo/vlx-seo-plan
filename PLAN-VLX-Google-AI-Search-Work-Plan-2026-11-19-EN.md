# VLX in Google AI Search — Findings & Dated Work Plan

**Date:** 2026-11-19 · **Source:** "VLX in Google AI Search — Findings and Recommendations" (HeyDay Marketing + PR, snapshot 2026-11-12, US English, Florida network, non-personalized) · **Live checks:** vlx.ai, 2026-11-13 and 2026-11-19 (curl)

---

## 1. Executive summary

- **Google knows VLX by name, not by category.** Brand searches ("Visualogyx", "VLX inspection") trigger AI Overview / AI Mode answers that correctly explain the rebrand and mention VLX Home. In **6 of 6 category searches** VLX is not named.
- **"VLX vs Spectora"** returns no AI answer; the SERP is dominated by Spectora pages, some labeled *"Missing: VLX"*.
- **Naming risk:** Google surfaced **VizaLogix**, a different product that also uses the "VLX" acronym.
- AI Overviews and AI Mode use **the same ranking systems as regular Search**. There is no special "AI optimization": the levers are expert first-hand content, genuine third-party mentions (forums, reviews, video), clean entity data, and measurement in Search Console.
- **Do not invest in:** `llms.txt`, special "AI schema", content chunking, or paid mention schemes. Google says these are unnecessary or ineffective.

## 2. What the report found

### 2.1 How Google builds AI answers (official guidance, updated 2026-07-10)
1. Same ranking and quality systems as Search.
2. Only indexed pages that allow a snippet can be used. Google often runs several related searches (query fan-out) to collect pages.
3. Top factor: valuable, non-commodity content (unique view, first-hand experience, expert insight).
4. The wider web counts: blogs, videos and forums that talk about products.
5. Measurement: Search Console → Settings → *Search generative AI* (default: Include) + the *Generative AI* report under Performance (impressions by page, country, device; **no clicks**).

*Not from Google (directional):* Reddit, YouTube, LinkedIn and review sites appear often as sources; AI Mode and AI Overviews often cite different pages.

### 2.2 Brand vs. category visibility
| Query type | Result |
|---|---|
| "Visualogyx" | The only standard-page AI Overview in 22 searches |
| "Visualogyx", "VLX inspection" (AI Mode) | Correct rebrand story; VLX Home mentioned. Sources: vlx.ai, rebrand post, App Store, YouTube, Yahoo Finance, Software Advice, Capterra |
| home inspection software · best home inspection software · field inspection software · inspection audit software · digital inspection app · quality inspection software manufacturing | **VLX not named** |
| VLX vs Spectora · Visualogyx vs Spectora | No AI answer; Spectora pages, "Missing: VLX" |

### 2.3 Two markets, two competitor sets
| Market | Named in AI Mode | Sources Google used |
|---|---|---|
| **VLX Home** (launched 2026-10-20, Early Access) | Spectora, HomeGauge, Home Inspector Pro, ScribeWare, TapInspect, Inspector Toolbelt, InterNACHI Practice Software | InterNACHI Forum, r/homeinspectors, product sites, Inspector Outlet, YouTube |
| Enterprise — inspection audit | SafetyCulture (iAuditor), Lumiform, GoAudits, EASE, EHS Insight, Fluix | — |
| Enterprise — digital inspection app | SafetyCulture, GoAudits, Novade, Dashpivot, MaintainX, UpKeep, GoCanvas | — |
| Enterprise — field inspection | zInspector, InspectorData, Fulcrum, Cloudpermit | — |
| Enterprise — manufacturing quality | 1factory, ATS Inspect, SOLIDWORKS Inspection, Mitti (SafetyCulture), Xenia | — |

Spectora is the right benchmark for VLX Home and the wrong one for core enterprise VLX.

### 2.4 Research limits
Single-day snapshot. One enterprise AI Mode query was blocked ("inspection and compliance software for enterprises"). **Not yet tested:** collateral inspection software, inspection fraud detection, wind mitigation software, TREC inspection software.

## 3. Live verification on vlx.ai (our checks)

| Item | 2026-11-13 | 2026-11-19 | Status |
|---|---|---|---|
| `vlx.ai/internachi/` | 404 | **200**, indexable, title "InterNACHI Member Discount: Home Inspection Software" | ✅ Fixed. Minor: the InterNACHI blog post still links `/internachi` (no slash) → 308 hop |
| Founding year | `/about-us/` "2018 (as Visualogyx)" vs schema `foundingDate: 2020-01-01` | Unchanged | 🔴 Open — needs a business decision |
| Organization `alternateName` | — | `"Visualogyx"` present (Organization + WebSite) | ✅ Done |
| Organization `sameAs` | — | LinkedIn, YouTube, Instagram, X, Facebook, G2 | 🟠 Missing App Store, Capterra, Software Advice (all cited by Google) |
| Rebrand content | 2 URLs in sitemap | Press URL and blog URL, each self-canonical | 🟠 Overlap — decide primary |
| `/compare/vlx-vs-safetyculture/` (+ goaudits) | — | 200 but **noindex, nofollow** | 🟠 Invisible to Search/AI |
| `robots.ts` blocks `Google-Extended` | — | — | ℹ️ Does **not** affect AI Overviews/AI Mode (they are Search features crawled by Googlebot). No change needed. |

## 4. Cross-check with work we had already defined

| Report recommendation | Already defined by us | New |
|---|---|---|
| 01 Choose categories | Keyword-ownership contract per service page (home inspection software, home inspector software, quality inspection software, field inspection software, inspection management software…) | Prioritize which categories/states to fight for in AI answers |
| 02 Genuine mentions | — | InterNACHI forum, r/homeinspectors, inspector lists; enterprise review/listing pages |
| 03 Expert first-hand pages | Template/checklist engine (`CONTENT-PROPOSALS-VLX-keyword-optimized-EN.md`, SafetyCulture gap at KD≈0); VLX Home specialty hub (wind mitigation, 4-point, TREC…); FAQ + FAQPage on `/home-inspection-software/` | Keep volume low, first-hand quality |
| 04 Reviews | — | Review-invitation program per category |
| 05 Fair comparisons | AI home inspection platforms comparison (published 2026-11-12); "Spectora alternative" post drafted 2026-11-19; `/compare/` enterprise pages exist (noindex); missing /vs-homegauge and /vs-home-inspector-pro | Legal review only for `/compare` and `/vs` pages (blog posts excluded); fair pricing comparisons (annual vs annual) |
| 06 Video | — | Expand YouTube per category + embed with `VideoObject` |
| 07 Naming/technical | `alternateName` already live; InterNACHI page now live | Founding year, `sameAs`, listing names, rebrand overlap, internal link hop |
| 08 Measure in GSC | GSC already used for SEO | AI control check + monthly Generative AI report |

## 5. Open decisions (owner: VLX / Laura)

| # | Decision | Needed by |
|---|---|---|
| D1 | Correct founding year (2018 or 2020) | 2026-11-25 |
| D2 | Target categories and US states (VLX Home, enterprise or both — separate tracks) | 2026-11-30 |
| D3 | Legal review of comparison pages (`/compare` enterprise + `/vs` VLX Home; blog posts excluded) | 2026-12-04 |
| D4 | Plan for retiring "Visualogyx" (how long to keep "formerly Visualogyx") | 2026-12-04 |
| D5 | Review-invitation policy | 2026-12-04 |
| D6 | VLX Home general availability timing (affects claims: payments/invoicing "coming soon") | 2026-12-11 |
| D7 | Ownership split: HeyDay (PR, communities, reviews, listings) vs SEO team (on-site, content, measurement) | 2026-11-25 |


## 6. Overlap check against existing plans (2026-11-19)

Every task below was checked against the other plans in this repo: the live dashboard (`index.html`, 99 tasks, status read from the dashboard on 2026-11-19), the post-launch plan (2026-07-22), the deep-audit backlog (2026-10-19), the June audit review, the Mitti/SafetyCulture audit, the content proposals, the VLX Home audit and the financial sub-pages proposal. Column **Prior plan**: 🔁 = already proposed elsewhere (execute it there, do not duplicate) · 🆕 = only in this plan.

**Result:** 31 tasks → **22 already proposed** in other plans (execute them under their existing ID), **9 new**.

| Already proposed (dashboard ID · status) | Covers |
|---|---|
| **S13** AI Overviews test · done, monthly | 1.1, 1.2, 2.7, 5.1, 5.2 |
| **S9** AI mentions test · backlog | 1.2, 5.1 |
| **A13** CountIt store rebrand · backlog · **S1** ASO · wip | 1.8 |
| **A14** claim G2/Capterra/GetApp + real reviews · backlog | 1.8, 3.4 |
| **P4** review acquisition · backlog · **P3** ASO reviews · backlog | 3.4 |
| **S15** listicle outreach · backlog · **A11** backlinks · backlog · **S37** sends | 3.3 |
| **G5** competitor audit + 5 comparison pages · backlog | 2.6 |
| **G3** FAQ/AEO · backlog | 2.1 |
| **G2** GEO hub + about copy · backlog · **O12** listicle · backlog · **O5** oil & gas | 4.6 |
| **G6** 50-keyword benchmark · backlog | 5.2 |
| **O13** / **O14** product landings & keyword targets · backlog | D2 |
| **HCU1/HCU2** forums + experience-first content (post-launch audit, not in current dashboard) | 3.1, 3.2, 4.6 |
| Content proposals Tier A/B, Mitti audit P0 | 4.1–4.3 |
| Mitti audit QW1, June review S3 (`foundingDate`) | 1.6, 1.7 |
| VLX Home audit / brief (hub, /vs pages, FAQ) | 2.1, 2.3–2.5, 4.4 |

**New in this plan:** 1.3 decisions memo · 1.4 pricing-claim fix · 1.5 InterNACHI link · 1.9 rebrand overlap · 2.2 legal review of `/compare` and `/vs` · 3.5 video plan · 3.6 `VideoObject` · 4.5 TREC page · 5.3 quarterly review · plus the 2018-vs-2020 founding-year conflict (1.7) and the GSC Generative AI report (1.1).

**Conflict to note:** `llms.txt` and `llms-full.txt` already exist (**A12**, **S11**, both done). This plan does not ask to remove them; it only recommends no further investment there.

## 7. Dated work plan

**Owners:** SEO = SEO team · DEV = web dev (VLX-Marketing, PR → dev → master) · HD = HeyDay · VLX = Laura / business · LEGAL
**Calendar notes:** US Thanksgiving 2026-11-26/27. Code freeze assumed 2026-12-21 → 2027-01-04 (content can still be prepared).

### Phase 1 — Quick fixes & baseline (2026-11-23 → 2026-12-04)

| ID | Task | Owner | Deliverable | Due | Prior plan |
|---|---|---|---|---|---|
| 1.1 | Confirm GSC *Search generative AI* = Include; export first Generative AI report (by page/country/device) | SEO | Baseline sheet (`data/gsc-generative-ai-baseline-2026-11.csv`) | 2026-11-24 | 🔁 Extends **S13** (done, monthly) + dashboard KPI "AI Overview citations"; adds the GSC Generative AI report |
| 1.2 | Run AI Mode / AI Overview tests: 6 category queries + 4 untested specialty queries + 2 "VLX vs Spectora" (US, logged out, screenshots + cited sources) | SEO | Query-test log v1 | 2026-11-25 | 🔁 Same as **S13** (done, monthly — 3 queries) and **S9** (backlog); run as next S13 cycle with the expanded query set |
| 1.3 | Decisions memo D1–D7 sent to VLX | SEO | 1-page memo | 2026-11-24 | 🆕 (D2 overlaps **O13**/**O14**, backlog) |
| 1.4 | Correct the "savings vs Spectora" pricing claim on `/home-inspection-software/` to a fair annual-vs-annual comparison | SEO → DEV | PR to dev | 2026-11-25 | 🆕 |
| 1.5 | Update InterNACHI post link to `/internachi/` (remove 308 hop) | SEO (CMS) | CMS edit | 2026-11-25 | 🆕 |
| 1.6 | Add App Store, Capterra, Software Advice (and other verified profiles) to Organization `sameAs` | DEV | PR to dev | 2026-12-02 | 🔁 Mitti audit QW1/P1 (complete Organization) — partly done (G2 already in `sameAs`) |
| 1.7 | Apply founding year (after D1) to `/about-us/` and `foundingDate` | DEV | PR to dev | 2026-12-02 | 🔁 June review **S3** (`foundingDate` format) + Mitti QW1; the 2018-vs-2020 conflict is new |
| 1.8 | "Formerly Visualogyx" consistency pass (about, footer, press, App Store, Capterra, Software Advice, G2, LinkedIn, YouTube) | HD + SEO | Checklist of listings with old/new names, all updated | 2026-12-04 | 🔁 **A13** (backlog, CountIt store rebrand), **S1** (wip, ASO), **A14** (backlog, claim G2/Capterra/GetApp) — do inside those |
| 1.9 | Rebrand overlap: choose primary URL (press vs blog), differentiate or canonicalize the other | SEO | Decision + CMS change | 2026-12-04 | 🆕 |

### Phase 2 — Comparisons & on-page (2026-12-01 → 2026-12-18)

| ID | Task | Owner | Deliverable | Due | Prior plan |
|---|---|---|---|---|---|
| 2.1 | Open/merge FAQ + FAQPage PR on `/home-inspection-software/` | SEO → DEV | Merged PR | 2026-12-03 | 🔁 Branch `feat/vlx-home-faq-automation-inspector-control`; related **G3** (backlog, FAQ/AEO) |
| 2.2 | Legal review of `/compare` enterprise pages and planned `/vs` pages (D3) | LEGAL | Approved claims list | 2026-12-04 | 🆕 |
| 2.3 | Publish "Spectora alternative" post (VLX Home vs Spectora), cross-linked with the 2026-11-12 comparison post | SEO | Live post | 2026-12-01 | 🔁 Post drafted 2026-11-19; VLX Home audit (2026-10-01) P2 "/vs-spectora link" |
| 2.4 | Draft VLX Home vs HomeGauge page | SEO | MD + PDF draft | 2026-12-10 | 🔁 VLX Home brief §18 (/vs-homegauge) |
| 2.5 | Draft VLX Home vs Home Inspector Pro page | SEO | MD + PDF draft | 2026-12-15 | 🔁 VLX Home brief §18 (/vs-home-inspector-pro) |
| 2.6 | Refresh `/compare/vlx-vs-safetyculture/` and `/compare/vlx-vs-goaudits/`, remove noindex after legal sign-off | SEO → DEV | PR to dev | 2026-12-17 | 🔁 **G5** (backlog, 5 comparison pages); June review E1 (/compare/vlx-vs-gocanvas) |
| 2.7 | Monthly AI visibility report #1 (GSC Generative AI + query-test log v2) | SEO | Report MD | 2026-12-18 | 🔁 **S13** monthly cycle |

### Phase 3 — Mentions, reviews & video setup (2026-12-01 → 2027-01-15)

| ID | Task | Owner | Deliverable | Due | Prior plan |
|---|---|---|---|---|---|
| 3.1 | Join InterNACHI forum with an official VLX profile; first genuine participation | HD | Profile + activity log | 2026-12-11 | 🔁 **HCU1** forum strategy (post-launch audit 2026-07-22; not in current dashboard) |
| 3.2 | Community presence plan for r/homeinspectors and Inspector Outlet (within each platform's rules) | HD | Plan | 2026-12-11 | 🔁 **HCU1** (Reddit, industry forums) |
| 3.3 | Pitch list of inspector-software roundups/lists (home) and enterprise listings already cited for "inspection audit software" | HD + SEO | Target list (URL, contact, angle) | 2026-12-18 | 🔁 **S15** (backlog, listicle outreach), **A11** (backlog), **S37** (outreach sends) |
| 3.4 | Review-invitation program (D5): process + first wave of invites per category (Capterra, G2, Software Advice, App Store) | HD + VLX | Policy + invite wave 1 | 2027-01-08 | 🔁 **A14** + **P4** (backlog, review acquisition), **P3** (ASO review responses), June review A3 (`aggregateRating`) |
| 3.5 | Video plan: 1 video per priority category (e.g., VLX Home walkthrough, inspection audit workflow) | HD + SEO | Video brief list | 2027-01-08 | 🆕 (only **S20**, done, linked YouTube to GSC) |
| 3.6 | Embed published videos on matching pages with `VideoObject` schema | DEV | PR to dev | 2027-01-15 | 🆕 |

### Phase 4 — Expert content in chosen categories (2027-01-05 → 2027-02-12)

Scope depends on D2. Rule from the report: **few, first-hand, expert pages — no mass production.**

| ID | Task | Owner | Deliverable | Due | Prior plan |
|---|---|---|---|---|---|
| 4.1 | Select first 6 templates/checklists from the in-app library to publish as web pages (priority by Ranki gap: SOP, fire extinguisher inspection, incident report, vehicle inspection checklist, RCA, risk assessment) | SEO + VLX | Shortlist + briefs | 2027-01-08 | 🔁 `CONTENT-PROPOSALS-VLX-keyword-optimized-EN.md` Tier A/B + Mitti audit P0 |
| 4.2 | Publish templates batch 1 (3 pages) | SEO | Live pages | 2027-01-22 | 🔁 Same (Tier A briefs A1/A3/A4/A5) |
| 4.3 | Publish templates batch 2 (3 pages) | SEO | Live pages | 2027-02-05 | 🔁 Same |
| 4.4 | VLX Home specialty page: wind mitigation inspection software (activate hub link) | SEO → DEV | Live page | 2027-01-29 | 🔁 VLX Home specialty hub (VLX Home audit 2026-10-01, §F) |
| 4.5 | VLX Home specialty page: TREC inspection software (Texas) — if D2 includes Texas | SEO → DEV | Live page | 2027-02-12 | 🆕 (TREC only planned as a report form, not as a page) |
| 4.6 | Enterprise pillar refresh for "inspection audit software" / "field inspection software" with first-hand customer examples | SEO | Updated pages | 2027-02-12 | 🔁 **G2** (backlog, GEO hub + about), **O12** (backlog, listicle), **HCU2** (experience-first content), **O5** (oil & gas) |

### Phase 5 — Measurement cadence (ongoing)

| ID | Task | Owner | Due | Prior plan |
|---|---|---|---|---|
| 5.1 | Monthly AI visibility report #2 | SEO | 2027-01-15 | 🔁 **S13**/**S9** (monthly) |
| 5.2 | Monthly AI visibility report #3 + full re-test of all queries; compare vs 2026-11-12 snapshot | SEO | 2027-02-15 | 🔁 **S13** + **G6** (backlog, 50-kw benchmark) |
| 5.3 | Quarterly review: keep/adjust categories, decide next content wave | SEO + VLX + HD | 2027-02-26 | 🆕 |

## 8. Milestones

| Date | Milestone |
|---|---|
| 2026-11-25 | Baseline + query tests done; decisions memo sent; pricing claim corrected |
| 2026-12-04 | Naming/technical clean-up complete (founding year, `sameAs`, listings, rebrand) |
| 2026-12-18 | Comparison layer live (Spectora alternative post, enterprise /compare indexable); report #1 |
| 2027-01-15 | Reviews, community and video programs running; report #2 |
| 2027-02-12 | First expert content wave live (6 templates + 1–2 specialty pages) |
| 2027-02-15 | Re-test vs baseline: is VLX named in any category AI answer? |

## 9. Success metrics

- **Primary:** VLX named/cited in AI Mode or AI Overview for ≥ 2 of the 6 tracked category queries by 2027-02-15 (directional; answers vary by day and location).
- GSC Generative AI impressions: month-over-month growth from the November baseline.
- "VLX vs Spectora" returns VLX-owned pages in the top 10.
- Reviews: new verified reviews per category per month (target set with D5).
- Zero naming inconsistencies across listings (founding year, "formerly Visualogyx").

## 10. Out of scope (per Google guidance)
`llms.txt` · special "AI schema" · content chunking for AI · paid mention schemes · mass-produced pages.
