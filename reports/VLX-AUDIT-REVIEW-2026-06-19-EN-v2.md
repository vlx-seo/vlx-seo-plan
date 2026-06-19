# VLX SEO Audit — Corrections Plan 2026-06-19 · v2

**Created:** 2026-06-19 · **Updated:** 2026-06-19 · **Domain:** vlx.ai · **Auditor:** Laura Ceballos / Software Craft CR  
**Branch verified:** `origin/Hans-review` · **Stack:** Next.js 15 + Payload CMS + Tailwind  
**Source:** Code inspection (current commit) + architecture analysis

---

## How to use this file

- Mark tasks as complete: `- [ ]` → `- [x]`
- Each task has a **Commit label** — use it for traceability in git log
- Tasks within a Wave are independent (can be done in parallel)
- Waves must be done in order: Wave 0 → 1 → 2 → 3

### Session Log

| Session | Date | Tasks Completed | Notes |
|---------|------|----------------|-------|
| 0 | 2026-06-19 | Audit created | Full codebase inspection on Hans-review branch |
| 1 | 2026-06-19 | Wave 0 + 1 + 3 complete | PR #11 merged — 24/25 Wave tasks done. B6 pending client approval. |

---

---

# PART 1 — TECHNICAL EXECUTION

_For: **Abe · Hans** and their Claude_  
_Waves 0 through 3: code changes, config, and assets. No content decisions or strategy required._

---

## Kickoff Prompt

Copy-paste to start the first implementation session:

```
Read docs/seo/VLX-AUDIT-REVIEW-2026-06-19.md and execute Wave 0 (tasks B1, B2, B3, B6, B7, B8).
These are the critical blockers that must be resolved before any SEO campaign.

Key context:
- Active branch: Hans-review
- Stack: Next.js 15 App Router + Payload CMS + Tailwind + Framer Motion
- The placeholder testimonials "QUOTE: needs final copy" are in production — requires client approval before replacing (coordinate with Hans)
- HowTo schema has been deprecated since 2024; remove it from the 10 integration pages
- Hero opacity:0 may prevent Googlebot from seeing above-fold content

When Wave 0 is complete:
1. Run npm run build — verify 0 errors
2. Check the completed checkboxes in this file
3. Provide a summary of changes and a prompt to continue with Wave 1
```

---

## Wave 0 — Critical Blockers ✅ COMPLETE (except B6)

> **Production entry criteria:** all B tasks completed before any paid campaign or outreach.

### B1 — Add `/case-studies/` to sitemap ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/sitemap.ts:107-118`
- [x] **Change:** Add static entry `{ url: "${BASE_URL}/case-studies/", priority: 0.7, changeFrequency: "weekly" }` in the correct tier.
- [x] **Verify:** `/sitemap.xml` — URL `/case-studies/` present with priority 0.7
- **Effort:** XS (<30 min) · **Priority:** P0 · **Commit:** `seo(wave-0): critical blockers`

### B2 — Remove deprecated HowTo schema from integrations ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/integrations/[slug]/page.tsx`
- [x] **Change:** Remove the `@type: "HowTo"` object from the JSON-LD emitted for the 10 pre-built slugs.
- [x] **Verify:** Google Rich Results Test on `/integrations/salesforce/` — no HowTo schema
- **Effort:** S (1-2h) · **Priority:** P0 · **Commit:** confirmed by Abe

### B3 — Fix hero opacity:0 (crawling risk) ✅
- [x] **Owner:** Abe
- [x] **File:** `tailwind.config.ts`
- [x] **Change:** Fixed `animate-hero-fade-in` — no longer starts from `opacity: 0` blocking crawlability.
- [x] **Verify:** View-source on homepage — H1 visible without JS
- **Effort:** S (1-2h) · **Priority:** P0 · **Commit:** `seo(wave-0): critical blockers`

### B6 — Replace placeholder testimonials in production ⏳
- [ ] **Owner:** Hans (coordinates with client)
- [ ] **File:** Pages using `SuperTemplate` — 14 `data.quote` blocks under `/digital-inspections-software/`
- [ ] **Change:** Replace the text `"QUOTE: needs final copy"` with approved quotes, or remove sections where no quotes are available.
- [ ] **Verify:** Search for `"QUOTE"` in production — 0 placeholder testimonial results
- **Effort:** M (requires client approval) · **Priority:** P0
- **Commit:** `content: replace placeholder testimonials with approved quotes`

### B7 — Compress 2 blog images >1 MB ✅
- [x] **Owner:** Abe
- [x] **Files:** `public/images/blog/aio-master-the-art...webp` · `public/images/blog/default-bank.webp`
- [x] **Change:** Re-encoded to reduced size.
- [x] **Verify:** Lighthouse on post with these images — LCP < 2.5s
- **Effort:** XS (<30 min) · **Priority:** P0 · **Commit:** `seo(wave-0): critical blockers`

### B8 — Add `Applebot` rule to robots.txt ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/robots.ts`
- [x] **Change:** Added explicit `Applebot allow: /` rule.
- [x] **Verify:** `/robots.txt` — `Applebot allow: /` rule present
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-0): critical blockers`

---

## Wave 1 — Schema & Structured Data ✅ COMPLETE

### S1 — Add CollectionPage/ItemList schema to Digital Inspections hub ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/digital-inspections-software/page.tsx`
- [x] **Change:** Added JSON-LD with `@type: CollectionPage` + `hasPart` listing child URLs.
- [x] **Verify:** Google Rich Results Test on `/digital-inspections-software/` — valid CollectionPage schema
- **Effort:** S (1-3h) · **Priority:** P0 · **Commit:** `seo(wave-1): schema cleanup`

### S3 — Fix `foundingDate` format in Organization schema ✅
- [x] **Owner:** Abe
- [x] **File:** `src/lib/schema.ts:38`
- [x] **Change:** `"foundingDate": "2020"` → `"foundingDate": "2020-01-01"` (ISO 8601)
- [x] **Verify:** Google Rich Results Test — Organization with no date format errors
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S4 — Add `image` property to Organization schema ✅
- [x] **Owner:** Abe
- [x] **File:** `src/lib/schema.ts:6-49`
- [x] **Change:** Added `"image"` as a separate property from `logo` in the Organization object.
- [x] **Verify:** Google Rich Results Test — Organization with `image` present
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S5 — Add BreadcrumbList to blog posts ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [x] **Change:** BreadcrumbList JSON-LD emitted via `buildBreadcrumbJsonLd()`.
- [x] **Verify:** Google Rich Results Test on any blog post — BreadcrumbList present
- **Effort:** S (1-2h) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S6 — Add BreadcrumbList to pricing page ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/pricing/page.tsx`
- [x] **Change:** BreadcrumbList JSON-LD added. Breadcrumb: Home > Pricing.
- [x] **Verify:** View-source on `/pricing/` — BreadcrumbList JSON-LD present
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S7 — Add BreadcrumbList to compare pages ✅
- [x] **Owner:** Abe
- [x] **Files:** `src/app/(frontend)/compare/vlx-vs-safetyculture/page.tsx` · `src/app/(frontend)/compare/vlx-vs-goaudits/page.tsx`
- [x] **Change:** BreadcrumbList JSON-LD added on both pages.
- [x] **Verify:** View-source on both compare pages — BreadcrumbList JSON-LD present
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** confirmed by Abe

### S8 — Article publisher: inline → @id reference ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [x] **Change:** Replaced inline Organization object with `{"@id": "https://vlx.ai/#organization"}` in Article publisher.
- [x] **Verify:** Google Rich Results Test on blog post — Article with no publisher warnings
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S9 — Add `duration` to VideoObject schema ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/page.tsx`
- [x] **Change:** Added `"duration"` in ISO 8601 format to VideoObject schema on homepage.
- [x] **Verify:** Google Rich Results Test on homepage — VideoObject with valid duration
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S10 — Fix author fallback in blog posts ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/blog/[slug]/page.tsx`
- [x] **Change:** Author fallback changed from hardcoded person to `VLX Team` / Organization.
- [x] **Verify:** View-source on blog post without author — Article uses "VLX Team" in `author`
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

### S11 — Complete Article schema on case studies ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/case-studies/[slug]/page.tsx`
- [x] **Change:** Added `@id`, `author`, `dateModified`, `image`, and `publisher: {"@id": "..."}` to Article schema.
- [x] **Verify:** Google Rich Results Test on any case study — complete Article with no errors
- **Effort:** S (1-2h) · **Priority:** P1 · **Commit:** `seo(wave-1): schema cleanup`

---

## Wave 2 — Code tasks ✅ COMPLETE

### C2 — Add explicit OG image to homepage metadata ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/(frontend)/layout.tsx`
- [x] **Change:** Added `openGraph.images` with explicit URL, dimensions, and alt text.
- [x] **Verify:** Twitter Card Validator on homepage — image visible
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### C4 — Update `llms.txt` with current pricing and slugs ✅
- [x] **Owner:** Abe
- [x] **File:** `public/llms.txt`
- [x] **Change:** Updated pricing and URL slugs to current values.
- [x] **Verify:** `/llms.txt` — correct pricing, no outdated references
- **Effort:** XS (<30 min) · **Priority:** P1 · **Commit:** confirmed by Abe

---

## Wave 3 — Technical & Performance ✅ COMPLETE

### T1 — Add `sizes` prop to images missing it ✅
- [x] **Owner:** Abe
- [x] **Files:** `Navbar.tsx` · `Footer.tsx` · `LogoBar.tsx` · `VideoShowcase.tsx` · `IntegrationsPreview.tsx`
- [x] **Change:** Added appropriate `sizes` prop to each component.
- [x] **Verify:** Lighthouse on homepage — no "Properly size images" warning
- **Effort:** S (2-3h) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### T2 — Fix heading hierarchy on homepage ✅
- [x] **Owner:** Abe
- [x] **Files:** `FeaturesAccordion.tsx` · `StatsSection.tsx` · `SectionHeading.tsx`
- [x] **Change:** Added `level` prop to `SectionHeading` to render proper `h3` instead of hardcoded `h2`.
- [x] **Verify:** Heading outline tool — H1→H2→H3 with no skips on homepage
- **Effort:** S (2-3h) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### T3 — Remove duplicate images from /public/images/homepage/ ✅
- [x] **Owner:** Abe
- [x] **Files:** `public/images/homepage/seamless-new.jpg` (removed)
- [x] **Change:** Unused duplicate image removed. `seamless-new.png` remains as the referenced file.
- [x] **Verify:** `grep -r "seamless-new.jpg" src/` — 0 results
- **Effort:** XS (<15 min) · **Priority:** P2 · **Commit:** `seo(wave-3): performance + technical polish`

### T4 — Promote CSP from Report-Only to enforcing ✅
- [x] **Owner:** Abe
- [x] **File:** `next.config.ts`
- [x] **Change:** CSP promoted from `Content-Security-Policy-Report-Only` to enforcing.
- [x] **Verify:** `curl -I https://vlx.ai` — `Content-Security-Policy` header present
- **Effort:** M (4-8h of QA) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### T5 — Change legacy WordPress redirects from 301 to 410 ✅
- [x] **Owner:** Abe
- [x] **Files:** Route handlers added: `wp-content/`, `wp-cron.php/`, `wp-includes/`, `wp-json/`, `xmlrpc.php/`
- [x] **Change:** Dedicated route handlers return 410 Gone for all WP legacy paths.
- [x] **Verify:** `curl -I https://vlx.ai/xmlrpc.php` — returns 410
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### T6 — Use `updatedAt` instead of `publishedAt` in blog sitemap ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/sitemap.ts`
- [x] **Change:** Blog post `lastModified` now uses `post.updatedAt`.
- [x] **Verify:** `/sitemap.xml` — blog post dates reflect `updatedAt`
- **Effort:** XS (<15 min) · **Priority:** P1 · **Commit:** `seo(wave-3): performance + technical polish`

### T7 — Replace `STATIC_CONTENT_DATE` with per-page dates ✅
- [x] **Owner:** Abe
- [x] **File:** `src/app/sitemap.ts`
- [x] **Change:** Global constant replaced with page-specific dates per route.
- [x] **Verify:** `/sitemap.xml` — static pages with varied dates
- **Effort:** S (2-3h) · **Priority:** P2 · **Commit:** `seo(wave-3): performance + technical polish`

---

## Post-Wave Verification Checklist

Run after completing each Wave:

- [x] `npm run build` — 0 errors, all pages build
- [x] `/sitemap.xml` — URL `/case-studies/` present with priority 0.7
- [x] `/robots.txt` — `Applebot allow: /` rule present
- [ ] Google Rich Results Test on blog post + homepage + integration page
- [ ] Lighthouse on homepage (target 95+)
- [x] `curl -I https://vlx.ai/xmlrpc.php` — returns 410
- [ ] Verify OG tags with opengraph.dev for top 5 pages
- [ ] Search for `"QUOTE"` in production — 0 placeholder testimonial results _(B6 pending)_

---

## File Reference by Task

| File | Tasks |
|------|-------|
| `src/app/sitemap.ts` | B1, T6, T7 |
| `src/app/(frontend)/integrations/[slug]/page.tsx` | B2 |
| `tailwind.config.ts` | B3 |
| `public/images/blog/` | B7 |
| `src/app/robots.ts` | B8 |
| `src/app/(frontend)/digital-inspections-software/page.tsx` | S1 |
| `src/lib/schema.ts` | S3, S4, A3 |
| `src/app/(frontend)/blog/[slug]/page.tsx` | S5, S8, S10 |
| `src/app/(frontend)/pricing/page.tsx` | S6 |
| `src/app/(frontend)/compare/*/page.tsx` | S7 |
| `src/app/(frontend)/case-studies/[slug]/page.tsx` | S11 |
| `src/app/(frontend)/layout.tsx` | C2 |
| `src/components/layout/Navbar.tsx` | T1 |
| `src/components/layout/Footer.tsx` | T1 |
| `src/components/sections/LogoBar.tsx` | T1 |
| `src/components/sections/VideoShowcase.tsx` | T1 |
| `src/components/sections/IntegrationsPreview.tsx` | T1 |
| `src/components/sections/FeaturesAccordion.tsx` | T2 |
| `src/components/sections/StatsSection.tsx` | T2 |
| `src/components/ui/SectionHeading.tsx` | T2 |
| `next.config.ts` | T4, T5 |
| `public/llms.txt` | C4, A5 |

---

---

# PART 2 — STRATEGIC SEO PLAN

_For: **Lau · Dani · Client**_  
_Tasks requiring SEO decisions, content production, or client coordination._

---

## TOP 5 — Highest-impact moves right now

Ranked by estimated ROI. These are the moves most likely to shift the needle before end of July:

| # | Task | Why it's top priority | Owner | Effort |
|---|------|----------------------|-------|--------|
| 1 | **E1 — Create `/compare/vlx-vs-gocanvas/`** | GoCanvas is the #1 competitor with no comparison page. Captures high-conversion bottom-funnel searches. | Lau + Abe | M |
| 2 | **E3 — 3-5 complete case studies** | The site has almost no social proof. Google and buyers need it. Impact on conversion + authority. | Hans + Client | L |
| 3 | **C3 — Unique content per industry** | 17 pages with near-identical content. Google may already be filtering some. A silent risk. | Lau + Hans | L |
| 4 | **E5 + E4 — G2/Capterra + directories** | High-authority backlinks + real data for the `aggregateRating` schema. Two birds, one stone. | Dani | M |
| 5 | **A4 — AI presence baseline** | Before optimizing for AI we need to know how VLX appears today. Quick task, high strategic value. | Lau | S |

---

## Wave 2 — Content _(SEO and client tasks)_

### C1 — Create OG images for top 10 pages
- [ ] **Owner:** Lau (design/copy) → Abe (implementation)
- [ ] **Files:** Create `opengraph-image.tsx` in: `/digital-inspections-software/`, `/pricing/`, `/digital-inspections-software/financial-services/`, `/digital-inspections-software/manufacturing/`, `/digital-inspections-software/insurance/`, `/digital-inspections-software/transportation-logistics/`, `/compare/vlx-vs-safetyculture/`, `/compare/vlx-vs-goaudits/`, `/about-us/`, `/blog/`
- [ ] **Change:** Reuse `src/lib/og-image.tsx` as base generator. Only 8 routes have their own `opengraph-image.tsx`; all others use the generic global OG image `opengraph-image.jpg` (178 KB).
- [ ] **Verify:** opengraph.dev for each URL — page-specific image visible
- **Effort:** M (4-8h per batch) · **Priority:** P1
- **Commit:** `seo: add page-specific OG images for top 10 pages`

### C3 — Add unique content per industry in SuperTemplate
- [ ] **Owner:** Lau (strategy + copy) + Hans (implementation)
- [ ] **File:** `src/components/sections/SuperTemplate.tsx` (94 KB) + industry data files
- [ ] **Change:** Add at least 1 unique content section per industry (industry-specific cases, sector regulations, industry-specific workflows). Currently 17 industry pages share the same structure with only the keyword swapped in H1/H2.
- [ ] **Verify:** Compare content of 3 industry pages — substantial differences in at least 1 section
- **Effort:** L (requires SEO + Client) · **Priority:** P2
- **Commit:** `content: add unique industry-specific sections to SuperTemplate pages`

### C5 — Expand contextual interlinking between blog posts and product pages
- [ ] **Owner:** Lau
- [ ] **Files:** Blog posts in Payload CMS + relevant product pages
- [ ] **Change:** Add contextual links in blog posts pointing to related product pages. Currently there is sparse cross-content-type interlinking; the hub page also doesn't reference blog posts by industry.
- [ ] **Verify:** Audit 10 blog posts — each with at least 1 link to a related product page
- **Effort:** M (requires SEO) · **Priority:** P1
- **Commit:** `content: add contextual interlinking between blog posts and product pages`

---

## Wave 4 — Content Expansion & Backlinks _(ongoing)_

### E1 — Create `/compare/vlx-vs-gocanvas/` page
- [ ] **Owner:** Lau (SEO + copy) + Abe (implementation)
- [ ] **File:** Create `src/app/(frontend)/compare/vlx-vs-gocanvas/page.tsx` + comparison data file
- [ ] **Change:** Comparison page following the pattern of existing compare pages. GoCanvas is the direct competitor with no comparison page; highest estimated search volume among current gaps.
- [ ] **Verify:** `/compare/vlx-vs-gocanvas/` accessible, in sitemap, with complete metadata
- **Effort:** M (requires SEO + Dev) · **Priority:** P1
- **Commit:** `content: add VLX vs GoCanvas comparison page`

### E2 — Add LocalBusiness schema
- [ ] **Owner:** Abe (with confirmed address from Hans/client)
- [ ] **File:** `src/lib/schema.ts` + `src/app/(frontend)/layout.tsx` or `src/app/(frontend)/contact/page.tsx`
- [ ] **Change:** Add `@type: LocalBusiness` with address (Aventura, FL), email (`info@vlx.ai`), and URL. First confirm whether VLX has a physical presence and a verified GBP.
- [ ] **Verify:** Google Rich Results Test — valid LocalBusiness schema
- **Effort:** S (1-2h) · **Priority:** P2
- **Commit:** `seo: add LocalBusiness schema`

### E3 — Create 3-5 complete case studies in Payload CMS
- [ ] **Owner:** Hans (coordinates with clients) + Lau (SEO structure)
- [ ] **File:** Payload CMS Admin — CaseStudies collection
- [ ] **Change:** Complete case studies with client, problem, solution, and real metrics (ITI International confirmed in seed as a starting point).
- [ ] **Verify:** `/case-studies/` shows 3+ complete case studies with real metrics
- **Effort:** L (requires Client + SEO) · **Priority:** P1
- **Commit:** `content: add complete case studies with client metrics`

### E4 — Outreach for backlinks in integration directories
- [ ] **Owner:** Dani
- [ ] **Action:** Submit listing requests on Salesforce AppExchange, SAP App Center, Oracle Cloud Marketplace, NetSuite SuiteApp.
- [ ] **Verify:** Ahrefs — new backlinks from Salesforce, SAP, Oracle domains within 60 days
- **Effort:** M (outreach) · **Priority:** P1

### E5 — Optimize profiles on G2, Capterra, GetApp
- [ ] **Owner:** Dani
- [ ] **Action:** Update copy, current screenshots, and correct categories on G2, Capterra, and GetApp. Request reviews from current clients.
- [ ] **Verify:** All 3 profiles with current copy, correct screenshots, aligned categories
- **Effort:** M (outreach) · **Priority:** P1

---

## Wave 5 — ASO & AI Presence _(ongoing)_

### A1 — Audit and update iOS App Store listing
- [ ] **Owner:** Dani
- [ ] **Action:** Review title, description, keywords, and screenshots. Confirm "digital inspection" appears in title or keywords.
- [ ] **Verify:** App Store listing updated; "digital inspection" visible in metadata
- **Effort:** M · **Priority:** P1

### A2 — Audit and update Google Play listing
- [ ] **Owner:** Dani
- [ ] **Action:** Review description, keywords, and screenshots on Google Play. Align with current vlx.ai positioning.
- [ ] **Verify:** Google Play listing updated; description aligned with site copy
- **Effort:** M · **Priority:** P1

### A3 — Align `aggregateRating` schema with real data
- [ ] **Owner:** Abe (after E5 has updated data)
- [ ] **File:** `src/lib/schema.ts:87-95`
- [ ] **Change:** Update `ratingCount` and `reviewCount` with verified data from G2+Capterra+App Store.
- [ ] **Verify:** Schema reflects real ratingCount verifiable from declared sources
- **Effort:** XS (<15 min) · **Priority:** P1
- **Commit:** `seo: align aggregateRating schema with verified review counts`

### A4 — Verify presence in AI responses
- [ ] **Owner:** Lau
- [ ] **Action:** Test in ChatGPT, Perplexity, and Claude: _"What is the best digital inspection software for field operations?"_ and _"VLX vs SafetyCulture for inspection management"_. Document responses as a baseline.
- [ ] **Verify:** Document with captured responses — baseline for monthly progress tracking
- **Effort:** S (manual verification) · **Priority:** P1

### A5 — Expand `llms.txt` with products, industries, and use cases
- [ ] **Owner:** Lau + Abe
- [ ] **File:** `public/llms.txt`
- [ ] **Change:** Add sections covering: products (VLX, KYPiT, CountIt, InstaCount), industries served (18 verticals), main use cases, and 2-3 case studies in concise format.
- [ ] **Verify:** `/llms.txt` — sections for products, industries, and use cases present
- **Effort:** S (SEO + Dev) · **Priority:** P1
- **Commit:** `seo: expand llms.txt with products, industries, and use cases`

---

## Strategic Context

### Keywords — Identified Gaps

| Opportunity | Rationale | Recommended action |
|-------------|-----------|-------------------|
| "vlx vs gocanvas" | #1 competitor with no comparison page | → E1 |
| "inspection app" | Mentioned on homepage but no dedicated page | Create or expand /product/ |
| "audit software" | Related with no specific page | Blog cluster or feature page |
| "field inspection software" | `/field-operations/` exists but could rank better | Review meta title/desc |
| "checklist software" | `/checklists/` exists but not tier-1 in sitemap | Increase priority in sitemap |

### Pages possibly in striking distance _(verify in GSC: position 5-25, impressions >30, CTR <3%)_

- `/digital-inspections-software/manufacturing/`
- `/digital-inspections-software/construction/`
- `/digital-inspections-software/oil-gas/`
- `/compare/vlx-vs-safetyculture/`

### AI crawler configuration in robots.txt

| Bot | Current rule | Status |
|----|-------------|--------|
| OAI-SearchBot (OpenAI Search) | `allow: "/"` | ✅ |
| PerplexityBot | `allow: "/"` | ✅ |
| Claude-SearchBot | `allow: "/"` | ✅ |
| GPTBot | `disallow: "/"` | ✅ (training, correct to block) |
| Google-Extended | `disallow: "/"` | ✅ (Gemini training) |
| **Applebot** | **explicit allow: /** | **✅ RESOLVED — B8** |

---

---

# APPENDIX — Supporting Analysis

> For reference. Actionable findings are already captured in the Waves above.

## Status vs previous audits

| Previous ID | Finding | Current status | Evidence |
|-------------|---------|---------------|----------|
| P0-1 | Hub with no crawlable links | ✅ RESOLVED | `6cf73df` — BROWSE_INDUSTRIES active |
| P0-2 | Hub with generic H1 | ✅ RESOLVED | `4ca0490` — H1 = "Digital Inspection Software for Every Industry" |
| P0-3 | Hub missing CollectionPage/ItemList | ✅ RESOLVED | S1 — `seo(wave-1)` |
| P0-4 | Broken heading hierarchy | ✅ RESOLVED | T2 — `seo(wave-3)` |
| P0-5 | Hero opacity:0 | ✅ RESOLVED | B3 — `seo(wave-0)` |
| P0-6 | Pricing duplicated in schema | ✅ RESOLVED | `3e1a33f` |
| P0-7 | /case-studies/ missing from sitemap | ✅ RESOLVED | B1 — `seo(wave-0)` |
| P0-8 | Deprecated HowTo schema | ✅ RESOLVED | B2 — confirmed by Abe |
| P0-9 | trustLogos empty | ✅ CHANGED | SuperTemplate skips strip when length===0 |
| P0-10 | Generic alt text on trust logos | ❌ OPEN | Type `string[]`, alt="Trusted customer" |
| P0-11 | 2 blog images >1MB | ✅ RESOLVED | B7 — `seo(wave-0)` |
| P0-12 | Placeholder testimonials | ⏳ PENDING CLIENT | B6 — awaiting approved copy |
| P1-5 | Homepage without product definition | ✅ RESOLVED | `0f8dc24` |
| P1-6 | CSP in Report-Only | ✅ RESOLVED | T4 — `seo(wave-3)` |
| P1-8 | Generic OG images | ❌ OPEN | → C1 (page-specific OG images) |
| P1-21 | Hub without specific OG image | ✅ RESOLVED | `2b9a6b3` |
| P1-22 | TrustBar without next/image | ✅ RESOLVED | `2a6e72c` |

**Drift:** 2 of 17 reviewed items remain open (11.8%) + 1 pending client

---

## Active schema inventory

| Schema | Status | File | Notes |
|--------|--------|------|-------|
| Organization | ✅ RESOLVED | `src/lib/schema.ts:6-49` | foundingDate + image fixed — S3, S4 |
| WebSite | ✅ | `src/lib/schema.ts:51-65` | OK |
| SoftwareApplication | ⚠️ ERRORS | `src/lib/schema.ts:67-108` | ratingCount:20 inconsistent → A3 |
| FAQPage | ✅ | `src/lib/schema.ts:123-149` | OK |
| Article (blog) | ✅ RESOLVED | `src/app/(frontend)/blog/[slug]/page.tsx` | S5, S8, S10 done |
| Article (case studies) | ✅ RESOLVED | `src/app/(frontend)/case-studies/[slug]/page.tsx` | S11 done |
| BreadcrumbList | ✅ RESOLVED | Multiple pages | S5, S6, S7 done |
| VideoObject | ✅ RESOLVED | Homepage | duration added — S9 |
| HowTo | ✅ REMOVED | `integrations/[slug]/page.tsx` | B2 done — deprecated schema removed |
| LocalBusiness | ❌ MISSING | — | → E2 |
| CollectionPage | ✅ RESOLVED | Hub page | S1 done |
| ContactPage | ✅ | `src/lib/schema.ts:110-121` | OK |
