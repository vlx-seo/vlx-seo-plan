# VLX — Deep Site Audit + Pending Backlog (EN)

**Audit date:** 2026-10-19 · **Live re-validation:** 2026-10-22 · **Method:** LIVE (crawl of the 165 sitemap URLs + dashboard read).
**Note:** GSC/GA4 over MCP was unavailable (seo-server down) → everything verified with curl / browser. Ranking/impression figures come from prior work (Ranki + GSC export).

> Every item below was re-checked live on **2026-10-22**. Status key:
> **✅ RESOLVED** · **🔧 FIX IN OPEN PR** (branch `seo/quickfix-mold-redirect-and-blog-quality-h1`, not yet merged → still live) · **⏳ STILL OPEN** · **🧊 FROZEN** (by Laura's decision) · **ℹ️ BY DESIGN**.

---

## Technical verdict

The site is **technically very clean**. The crawl of the 165 sitemap URLs returns **164 at 200, each self-canonical, one H1, title present. 0 canonical mismatches, 0 wrong noindex, 0 missing titles/H1.**

**Service-page cannibalization stays resolved and deployed** (reaudit 2026-10-14): the 19 pages under `/digital-inspections-software/` keep the primary keyword unified across title + meta description + H1 (the hub is the only intentional exception, with a plural H1). No vertical competes for the hub head term anymore. **Confirmed still true 2026-10-22.**

---

## A. New findings from the audit

1. **✅ RESOLVED — Dead URL in the sitemap.** `/blog/digital-inspection-app-ai-human-review/` → still **404**, but it is **no longer listed in the sitemap** (re-checked: 0 occurrences). Optional leftover: serve **410** instead of 404.
2. **🔧 FIX IN OPEN PR — Two identical `<h1>` on `/blog/`.** Live HTML still carries **2 `<h1>`** ("Insights & Updates") as of 2026-10-22. Fix (demote the SSR fallback placeholder to `<p>`) is in the open PR, not yet merged.
3. **⏳ STILL OPEN (minor) — Coarse `lastmod` in the sitemap.** 166 entries, 115 distinct, but clustered on 3 bulk dates (52× 2026-06-18, 48× 2026-05-08, 45× 2026-07-15) → reflects bulk operations, not real content-update dates. Weak freshness signal. → Emit real per-page `lastmod`.
4. **⏳ STILL OPEN (cosmetic) — Apex redirect with explicit port.** `http://vlx.ai` → 301 → `https://vlx.ai:443/` (still). Works; the `:443` is cosmetic.

## B. Content duplicates (known, re-confirmed live)

5. **🔧 FIX IN OPEN PR — Broken "mold" redirect.** `/blog/checklists/mold-inspection-checklist/` → **308** → `/checklists/mold-inspection-checklist/` which is **404** (live canonical is `/blog/mold-inspection-checklist/`, 200). Still broken live 2026-10-22; fix is in the open PR.
6. **⏳ STILL OPEN — "7-modern-inspection-methods" duplicate.** Both `/blog/7-modern-inspection-methods/` and `/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/` return **200 and are both in the sitemap**. → Merge + 301 to the winner.
7. **⏳ STILL OPEN — supply-chain overlap.** `/blog/9-key-benefits-of-supply-chain-management/` + `/blog/why-supply-chain-management-software-is-essential-for-modern-logistics/` both **200** (≈84k + 8k impressions, ~0 clicks). → Consolidate + re-point to informational intent.

## C. Measurement

8. **🧊 FROZEN (confirmed down) — Home has no GTM/GA4/Pixel.** Live home: 0 `googletagmanager`, no `GTM-` container, 0 `fbq`. Matches the dashboard KPI note ("measurement dead since ~August"). Tasks **T4 + S27**. Left as-is per Laura's instruction — reported only.

## D. Performance

9. **⏳ STILL OPEN — S22 LCP image** at `w=1080&q=75` (not verifiable by curl; measure with PageSpeed/CrUX).
10. **⏳ STILL OPEN — S25** 64 images return 500 (fix branch written, no PR) · **S23** no CDN in front of vlx.ai (no edge/cache headers seen on live responses — consistent with origin-only serving).

## E. Schema

11. **ℹ️ BY DESIGN — `/pricing/` has no `SoftwareApplication`/`AggregateOffer`** (0 live). This is **intentional**: the canonical `SoftwareApplication` + `AggregateOffer` entity is emitted **once on the homepage** (`src/lib/schema.ts`, `@id #software`) — confirmed live on the home (SoftwareApplication + AggregateOffer present). Adding it to `/pricing/` would reintroduce the P0-6(d) `@id` collision. Not a bug. · **✅ L13 RESOLVED** — `/blog/` CollectionPage now emits **14 `ListItem`** (no longer capped at 12).

---

## Dashboard status (vlx-seo-plan)

**99 tasks → 26 done · 1 in progress (S28, VT-9376) · 72 backlog** (read from Supabase).

**Marked backlog but already true live (closable):** L1 (robots allows Google/Bing/Applebot — confirmed), L7 (hub hreflang + x-default live — confirmed), L2 (prod serves index,follow), apex http→https (301). **S24 (ISR)** likely closable (reverted; team moved to on-demand revalidation). **S5** (logo alts carry company names) and **S8** (industry H1s keyword-first) confirmed done live.

### Still-open quick wins (fastest first)
1. **🔧 In the open PR:** mold redirect (B5), `/blog/` double H1 (A2), `/quality/` H1 (drop "Digital"). → merge to deploy.
2. **⏳ Content:** merge "7-modern" duplicate (B6, a 301 — fastest) and supply-chain (B7); add CTAs to the 22 meta descriptions (S7).
3. **⏳ Console / infra (no PR):** reindex optimized pages in GSC (S19), add the YouTube channel to GSC (S20), restart ECS for the LCP image (S22).

### Larger pending work
Phase 2 Optimization (0/14: striking-distance rebuilds O1–O5, internal linking O10, "Best Digital Inspection Software 2026" listicle O12, product-landing rework O13, programmatic O11), Phase 3 Growth & AI (0/6: competitor audits G4/G5, comparison pages, GEO/AEO), Phase 4+ (0/6).

---

### Summary of what changed since the 2026-10-19 audit
- **Resolved:** dead URL out of the sitemap (A1), `/blog/` CollectionPage (L13), logo alts (S5), industry H1s (S8).
- **Fix built, awaiting merge (still live):** mold redirect (B5), `/blog/` double H1 (A2), `/quality/` H1.
- **Clarified as by-design:** `/pricing/` schema (O6) — the entity lives on the homepage.
- **Still open:** 7-modern & supply-chain duplicates, measurement (frozen), LCP image, 64 images 500, no CDN, coarse lastmod, cosmetic apex `:443`.
