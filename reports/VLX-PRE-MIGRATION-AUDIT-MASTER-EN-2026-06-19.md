# VLX Pre-Migration SEO Audit — MASTER REPORT
**Date:** 2026-06-19 · **Auditor:** Laura Ceballos / Software Craft CR
**Subject:** Next.js 15 + Payload CMS rebuild (branch `Hans-review` = future `dev`), pre-launch
**Baseline:** `vlx.ai` (WordPress + Yoast, still in production) · **Markets:** USA + Canada · **Language:** English

---

## 1. Verdict: GO — with conditions

**The new site is technically healthier than the WordPress site it replaces.** It server-renders cleanly, has near-perfect canonicals and redirects, richer schema, and dramatically better homepage performance. **There is no catastrophic blocker.** Launch is safe once a short list of pre-launch fixes is done — most are small.

**SEO Health Score: 81/100** (pre-launch readiness)

| Dimension | Score | Verdict |
|---|---|---|
| Technical (crawl/SSR/canonical) | 88 | ✅ Strong |
| Architecture (design) | 88 | ✅ Strong |
| Migrations / redirects | 86 | ✅ Strong |
| Schema / JSON-LD | 83 | ✅ Good (1 rating risk) |
| GEO / AI readiness | 82 | ✅ Good |
| Sitemap & robots | 80 | ✅ Good (sitemap 308s) |
| Images | 80 | ✅ Good |
| Content & E-E-A-T | 79 | 🟡 Good, improving |
| Performance | 76 | 🟡 Hub LCP issue |
| Internal-linking activation | 72 | 🟡 Distribution gaps |

*(Google baseline, Keywords/Backlinks, ASO/Local are strategy inputs, not scored.)*

---

## 2. What CANNOT be skipped before migrating

These are the must-fix items. None is catastrophic; all are concrete and mostly quick.

| # | Must-fix | Why | Effort | Report |
|---|---|---|---|---|
| 1 | **Fix hub LCP 7.9s** (`/digital-inspections-software/`) | The #1 commercial page is slower than the WP page it replaces; head-term page shouldn't launch at 7.9s LCP | M | PERF-P1-1 |
| 2 | **Resolve hardcoded `aggregateRating` 4.9/20** — use real Capterra/SoftwareAdvice review data or remove | Fabricated rating without on-page reviews = Google manual-action risk. Real reviews exist on Capterra (use them) | S | SCHEMA-P1-1 / LOCAL-1 |
| 3 | **Remove 5 redirecting `/blog/category/*` URLs from sitemap** | Sitemap lists URLs that 308 → Google flags "Page with redirect", won't index | XS | SR-P1-1 |
| 4 | **Add redirects for 2 `/use-case/` 404s** | Two old WP URLs hard-404 on launch (low traffic, but hygiene) | XS | MIG-P0-1 |
| 5 | **Confirm all paid landing pages redirect** | Capterra/G2 ad spend lands on 404 if not mapped (~150 paid sessions/mo) | S | MIG-P1-1 |
| 6 | **Fix homepage H1 "VerificationInspections" spacing** | Malformed primary H1 (animation concatenation) | XS | TECH-P1-2 |
| 7 | **Server-render blog listing + crawlable pagination** | 109 posts; only page 1 crawlable via links → deep posts rely on sitemap | M | TECH-P1-1 / SR-P1-2 |

**Recommended before launch (high value, not strictly blocking):**
- Align the 4 copywriting industry H1s (construction, safety, hospitality, mining) to keyword-led — the other 8 already are (CONTENT-P1-1).
- Add 6 missing industries to nav dropdown + footer (vehicle, construction, safety, hospitality, mining, inspection-companies) — they're the bottom of the internal-link graph (LINKING P1).
- Confirm named testimonials are client-approved (CONTENT-P1-2).

---

## 3. What's already done well (don't touch)

- **Crawlability/SSR:** every page type renders H1 + body + links in raw HTML; Googlebot gets everything.
- **Canonicals + redirects:** absolute, trailing-slash, matching sitemap; 52/54 old URLs redirect in a clean **1 hop** for the indexed (slash) shape. The old "3-hop chain" fear does not reproduce.
- **Homepage performance transformed:** LCP 20.5s (WP) → 2.7s (Next).
- **Schema:** CollectionPage hub, Article case studies, BreadcrumbList sitewide, HowTo removed, deprecated SearchAction gone.
- **robots.txt:** deliberate AI posture (search bots allowed, training bots blocked) + llms.txt complete.
- **Architecture:** hub now links all 18 clusters in-body (prior P0 fixed), click depth 2, keyword-rich anchors.
- **Industry H1s:** 8/12 now keyword-led (was the big prior gap).
- **Migration redirects:** 284 rules, 152 from real GSC impression data.

---

## 4. Pre-launch checklist (consolidated)

**Blockers / must-fix:**
- [ ] Hub LCP 7.9s → profile + preload LCP element, re-test on Hans-review build
- [ ] aggregateRating → real review counts (Capterra/G2) on-page, or remove
- [ ] Remove 5 redirecting category URLs from `sitemap.ts`
- [ ] Add 2 `/use-case/` redirects
- [ ] Verify paid-LP redirects (pull GA4 paid landing pages)
- [ ] Fix homepage H1 spacing
- [ ] SSR blog listing + path-based pagination

**Strongly recommended:**
- [ ] 4 industry H1s → keyword-led
- [ ] 6 industries into nav + footer
- [ ] Testimonials client-approved
- [ ] Page-specific OG images for top 10 pages

**At cutover:**
- [ ] Submit `sitemap.xml` in GSC; confirm production robots.txt = Hans-review version
- [ ] Monitor GSC Coverage (404/redirect) + USA clicks (≥100/28d) + organic sessions (≥330/28d) for 2-4 weeks
- [ ] Re-run PSI on production build

---

## 5. Pre-launch baseline to protect (freeze)

| Metric (28d, pre-launch) | Value |
|---|---|
| GSC clicks — global / USA / CAN | 341 / 121 / 11 |
| GSC impressions — global / USA / CAN | 86,494 / 37,683 / 2,253 |
| GSC avg position — USA / CAN | 24.8 / 15.1 |
| GA4 sessions / organic | 2,335 / 389 |

⚠️ Only ~39% of traffic is in-market (USA+CAN); global impressions are inflated by Vietnam/India/Indonesia. Judge success on the USA+CAN slice.

---

## 6. Post-launch strategy (the real growth levers)

The migration **protects and improves** the site, but ranking for commercial terms needs work the rebuild alone won't deliver:

1. **Backlinks / authority — the binding constraint.** Bing reports 0 inbound links; domain is ~9 months old. Priority: G2/Capterra/GetApp profiles, integration directories (Salesforce/SAP/Oracle), digital PR (plan E4/E5).
2. **Win the brand SERP** for "vlx" (currently pos 10.6) — entity + sameAs + GBP.
3. **Striking-distance quick wins** — 13 checklist queries at pos 4-20 with 0 clicks; optimize titles/CTR.
4. **Rank the hub** for "digital inspection software" — needs depth (currently ~700 words vs competitors' 6,000+) + authority.
5. **AI visibility baseline** — run the 6 prompts in the GEO report across ChatGPT/Perplexity/AI Overviews; track monthly.
6. **Apps + reviews** — rebrand Visualogyx→VLX listings, harvest Capterra reviews into schema.

---

## 7. Tooling notes / open data gaps
- **DataForSEO MCP returned 401** (unauthenticated) — re-auth needed to pull backlink profile, keyword volume/difficulty, and live competitor SERP. This audit used real GSC ranking data instead (better for current state, but no volume/competitor depth).
- **CrUX field data unavailable** for vlx.ai (below traffic threshold) — performance judged on lab (PSI) + should rely on Vercel Speed Insights / PostHog RUM post-launch.
- **Vercel preview = `dev` branch** (lacks PR #11 perf micro-fixes) — code audit ran on `localhost:3000` (Hans-review, accurate); external lab metrics will improve slightly on the real build.

---

## Appendix — Dimension reports
`AUDIT-MIGRATIONS`, `AUDIT-TECHNICAL`, `AUDIT-SCHEMA`, `AUDIT-SITEMAP-ROBOTS`, `AUDIT-CONTENT`, `AUDIT-GEO`, `AUDIT-IMAGES`, `AUDIT-PERFORMANCE`, `AUDIT-GOOGLE-BASELINE`, `AUDIT-KEYWORDS-BACKLINKS`, `AUDIT-LINKING-ARCH`, `AUDIT-ASO-LOCAL` (all dated 2026-06-19, in `/reportes/`). Raw data in `/data/`, inventory in `/inventario/`.
