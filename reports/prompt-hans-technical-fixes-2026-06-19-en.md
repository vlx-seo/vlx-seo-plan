# PROMPT — VLX Pre-Migration Technical Fixes (for Hans/Abe + their Claude)

> **How to use:** paste everything below into a Claude Code session open on the **VLX-Marketing** repo (branch `Hans-review`). These are the CODE fixes that must be done BEFORE migrating from WordPress to Next.js. From the SEO pre-migration audit 2026-06-19 (Laura / Software Craft CR).

---

You are a senior dev in the VLX-Marketing repo (Next.js 15 App Router, `src/app/(frontend)`, Payload CMS, Tailwind, Framer Motion; branch `Hans-review`). Implement the technical SEO fixes from the 2026-06-19 pre-migration audit. The site has NOT migrated yet (production is still WordPress vlx.ai). Implement each fix, verify, and report. Ask before destructive changes. Market: USA + Canada, English.

## Fixes (by priority)

### P0 — Blockers
1. **Hub LCP 7.9s** (the #1 commercial page). `/digital-inspections-software/` has 7.9s mobile LCP (worse than the WP version). Find the LCP element (likely the hero image), preload it (`<link rel="preload">` or `priority` on next/image), and make sure it's NOT behind an `opacity:0` animation. Target LCP < 2.5s. Re-measure with mobile Lighthouse.

2. **Fabricated aggregateRating** in `src/lib/schema.ts` (~lines 91-97): `ratingValue:"4.9", ratingCount:"20", reviewCount:"20"` hardcoded with no on-page reviews = Google manual-action risk. Option A: pull real counts from Capterra/SoftwareAdvice (reviews exist) and surface reviews on-page. Option B (fast): remove the `aggregateRating` block. Do NOT launch with a fabricated rating.

3. **Sitemap lists 5 redirecting URLs** in `src/app/sitemap.ts`. Of the 9 `/blog/category/*`, these 5 return 308: `case-studies`, `checklists`, `forms`, `product-updates`, `reports`. Emit ONLY the 4 that return 200 (`guides`, `industry-trends`, `perspectives`, `use-cases`). Verify every sitemap URL returns 200.

4. **2 /use-case/ URLs 404** on launch. Add redirects in `next.config.ts`: `/use-case/a-guide-to-prevent-insurance-fraud` → `/digital-inspections-software/insurance/` and `/use-case/strengthening-trust-in-financial-lending-the-role-of-digital-inspections` → `/digital-inspections-software/financial-services/` (or migrate the posts into the CMS if the content is worth keeping).

5. **Paid landing pages without redirects.** Check GA4 (landing pages with Capterra/G2 traffic) and confirm each paid LP has a redirect in `next.config.ts`. Otherwise day-1 ad spend hits 404s.

### P1
6. **Homepage H1 "VerificationInspections"** — the H1 renders "Verification" and "Inspections" concatenated (animation span with no space/fallback in SSR). Fix so the primary H1 reads clean in the raw HTML.

7. **Blog listing/pagination client-side.** The blog index and category pages render client-side; pagination is `?page=N` (not crawlable). Implement path-based `/blog/page/N/` pagination with real server-rendered `<a href>` links. (~109 posts; today only page 1 is crawlable via links.)

8. **4 industry H1s are copywriting** → make keyword-led. In the data files (`*Content.tsx`): construction → "Construction Inspection Software for Jobsite Safety & QA"; safety → "Safety Inspection Software for EHS Teams"; hospitality → "Hospitality Inspection Software for Brand-Standard Audits"; mining → "Mining Inspection Software for Safety & Compliance". (The other 8 industries are already keyword-led.)

9. **6 industries missing from nav + footer:** vehicle, construction, safety, hospitality, mining, inspection-companies. Add them to the nav dropdown (`Navbar.tsx`) and footer (`Footer.tsx`) — today they lose one sitewide internal link per page.

10. **Page-specific OG images** for the top 10 pages (hub, compare ×2, top industries, about-us, blog). Only 8 routes have their own `opengraph-image`; the rest use the generic one. Reuse `src/lib/og-image.tsx`.

## When done
1. `npm run build` — 0 errors. 2. `curl -I` every sitemap URL → all 200. 3. Mobile Lighthouse on the hub → LCP < 2.5s. 4. Report what's done and what needs a decision (e.g. real reviews for aggregateRating). Do NOT launch until P0 pass validation.

> SEO note (Laura): the "308 vs 301" redirect issue is NOT a blocker — Google treats 308 = 301. Don't prioritize it; prioritize P0-1 through P0-5.
