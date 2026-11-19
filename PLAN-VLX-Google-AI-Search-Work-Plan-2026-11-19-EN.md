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
| 05 Fair comparisons | AI home inspection platforms comparison (published 2026-11-12); "Spectora alternative" post drafted 2026-11-19; `/compare/` enterprise pages exist (noindex); missing /vs-homegauge and /vs-home-inspector-pro | Legal review gate; fair pricing comparisons (annual vs annual) |
| 06 Video | — | Expand YouTube per category + embed with `VideoObject` |
| 07 Naming/technical | `alternateName` already live; InterNACHI page now live | Founding year, `sameAs`, listing names, rebrand overlap, internal link hop |
| 08 Measure in GSC | GSC already used for SEO | AI control check + monthly Generative AI report |

## 5. Open decisions (owner: VLX / Laura)

| # | Decision | Needed by |
|---|---|---|
| D1 | Correct founding year (2018 or 2020) | 2026-11-25 |
| D2 | Target categories and US states (VLX Home, enterprise or both — separate tracks) | 2026-11-30 |
| D3 | Legal review of comparison content (Home + enterprise) | 2026-12-04 |
| D4 | Plan for retiring "Visualogyx" (how long to keep "formerly Visualogyx") | 2026-12-04 |
| D5 | Review-invitation policy | 2026-12-04 |
| D6 | VLX Home general availability timing (affects claims: payments/invoicing "coming soon") | 2026-12-11 |
| D7 | Ownership split: HeyDay (PR, communities, reviews, listings) vs SEO team (on-site, content, measurement) | 2026-11-25 |

## 6. Dated work plan

**Owners:** SEO = SEO team · DEV = web dev (VLX-Marketing, PR → dev → master) · HD = HeyDay · VLX = Laura / business · LEGAL
**Calendar notes:** US Thanksgiving 2026-11-26/27. Code freeze assumed 2026-12-21 → 2027-01-04 (content can still be prepared).

### Phase 1 — Quick fixes & baseline (2026-11-23 → 2026-12-04)

| ID | Task | Owner | Deliverable | Due |
|---|---|---|---|---|
| 1.1 | Confirm GSC *Search generative AI* = Include; export first Generative AI report (by page/country/device) | SEO | Baseline sheet (`data/gsc-generative-ai-baseline-2026-11.csv`) | 2026-11-24 |
| 1.2 | Run AI Mode / AI Overview tests: 6 category queries + 4 untested specialty queries + 2 "VLX vs Spectora" (US, logged out, screenshots + cited sources) | SEO | Query-test log v1 | 2026-11-25 |
| 1.3 | Decisions memo D1–D7 sent to VLX | SEO | 1-page memo | 2026-11-24 |
| 1.4 | Correct the "savings vs Spectora" pricing claim on `/home-inspection-software/` to a fair annual-vs-annual comparison | SEO → DEV | PR to dev | 2026-11-25 |
| 1.5 | Update InterNACHI post link to `/internachi/` (remove 308 hop) | SEO (CMS) | CMS edit | 2026-11-25 |
| 1.6 | Add App Store, Capterra, Software Advice (and other verified profiles) to Organization `sameAs` | DEV | PR to dev | 2026-12-02 |
| 1.7 | Apply founding year (after D1) to `/about-us/` and `foundingDate` | DEV | PR to dev | 2026-12-02 |
| 1.8 | "Formerly Visualogyx" consistency pass (about, footer, press, App Store, Capterra, Software Advice, G2, LinkedIn, YouTube) | HD + SEO | Checklist of listings with old/new names, all updated | 2026-12-04 |
| 1.9 | Rebrand overlap: choose primary URL (press vs blog), differentiate or canonicalize the other | SEO | Decision + CMS change | 2026-12-04 |

### Phase 2 — Comparisons & on-page (2026-12-01 → 2026-12-18)

| ID | Task | Owner | Deliverable | Due |
|---|---|---|---|---|
| 2.1 | Open/merge FAQ + FAQPage PR on `/home-inspection-software/` | SEO → DEV | Merged PR | 2026-12-03 |
| 2.2 | Legal review of: Spectora alternative post, /compare enterprise pages, planned /vs pages (D3) | LEGAL | Approved claims list | 2026-12-04 |
| 2.3 | Publish "Spectora alternative" post (VLX Home vs Spectora), cross-linked with the 2026-11-12 comparison post | SEO | Live post | 2026-12-08 |
| 2.4 | Draft VLX Home vs HomeGauge page | SEO | MD + PDF draft | 2026-12-10 |
| 2.5 | Draft VLX Home vs Home Inspector Pro page | SEO | MD + PDF draft | 2026-12-15 |
| 2.6 | Refresh `/compare/vlx-vs-safetyculture/` and `/compare/vlx-vs-goaudits/`, remove noindex after legal sign-off | SEO → DEV | PR to dev | 2026-12-17 |
| 2.7 | Monthly AI visibility report #1 (GSC Generative AI + query-test log v2) | SEO | Report MD | 2026-12-18 |

### Phase 3 — Mentions, reviews & video setup (2026-12-01 → 2027-01-15)

| ID | Task | Owner | Deliverable | Due |
|---|---|---|---|---|
| 3.1 | Join InterNACHI forum with an official VLX profile; first genuine participation | HD | Profile + activity log | 2026-12-11 |
| 3.2 | Community presence plan for r/homeinspectors and Inspector Outlet (within each platform's rules) | HD | Plan | 2026-12-11 |
| 3.3 | Pitch list of inspector-software roundups/lists (home) and enterprise listings already cited for "inspection audit software" | HD + SEO | Target list (URL, contact, angle) | 2026-12-18 |
| 3.4 | Review-invitation program (D5): process + first wave of invites per category (Capterra, G2, Software Advice, App Store) | HD + VLX | Policy + invite wave 1 | 2027-01-08 |
| 3.5 | Video plan: 1 video per priority category (e.g., VLX Home walkthrough, inspection audit workflow) | HD + SEO | Video brief list | 2027-01-08 |
| 3.6 | Embed published videos on matching pages with `VideoObject` schema | DEV | PR to dev | 2027-01-15 |

### Phase 4 — Expert content in chosen categories (2027-01-05 → 2027-02-12)

Scope depends on D2. Rule from the report: **few, first-hand, expert pages — no mass production.**

| ID | Task | Owner | Deliverable | Due |
|---|---|---|---|---|
| 4.1 | Select first 6 templates/checklists from the in-app library to publish as web pages (priority by Ranki gap: SOP, fire extinguisher inspection, incident report, vehicle inspection checklist, RCA, risk assessment) | SEO + VLX | Shortlist + briefs | 2027-01-08 |
| 4.2 | Publish templates batch 1 (3 pages) | SEO | Live pages | 2027-01-22 |
| 4.3 | Publish templates batch 2 (3 pages) | SEO | Live pages | 2027-02-05 |
| 4.4 | VLX Home specialty page: wind mitigation inspection software (activate hub link) | SEO → DEV | Live page | 2027-01-29 |
| 4.5 | VLX Home specialty page: TREC inspection software (Texas) — if D2 includes Texas | SEO → DEV | Live page | 2027-02-12 |
| 4.6 | Enterprise pillar refresh for "inspection audit software" / "field inspection software" with first-hand customer examples | SEO | Updated pages | 2027-02-12 |

### Phase 5 — Measurement cadence (ongoing)

| ID | Task | Owner | Due |
|---|---|---|---|
| 5.1 | Monthly AI visibility report #2 | SEO | 2027-01-15 |
| 5.2 | Monthly AI visibility report #3 + full re-test of all queries; compare vs 2026-11-12 snapshot | SEO | 2027-02-15 |
| 5.3 | Quarterly review: keep/adjust categories, decide next content wave | SEO + VLX + HD | 2027-02-26 |

## 7. Milestones

| Date | Milestone |
|---|---|
| 2026-11-25 | Baseline + query tests done; decisions memo sent; pricing claim corrected |
| 2026-12-04 | Naming/technical clean-up complete (founding year, `sameAs`, listings, rebrand) |
| 2026-12-18 | Comparison layer live (Spectora alternative post, enterprise /compare indexable); report #1 |
| 2027-01-15 | Reviews, community and video programs running; report #2 |
| 2027-02-12 | First expert content wave live (6 templates + 1–2 specialty pages) |
| 2027-02-15 | Re-test vs baseline: is VLX named in any category AI answer? |

## 8. Success metrics

- **Primary:** VLX named/cited in AI Mode or AI Overview for ≥ 2 of the 6 tracked category queries by 2027-02-15 (directional; answers vary by day and location).
- GSC Generative AI impressions: month-over-month growth from the November baseline.
- "VLX vs Spectora" returns VLX-owned pages in the top 10.
- Reviews: new verified reviews per category per month (target set with D5).
- Zero naming inconsistencies across listings (founding year, "formerly Visualogyx").

## 9. Out of scope (per Google guidance)
`llms.txt` · special "AI schema" · content chunking for AI · paid mention schemes · mass-produced pages.
