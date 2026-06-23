# VLX Pre-Migration Fixes — Verification (2026-06-23)

**By:** Laura Ceballos / Software Craft CR · **Scope:** `origin/dev` (Visualogyx/VLX-Marketing) · From the 2026-06-19 pre-migration SEO audit.

## Verdict: 9/10 resolved — hub LCP still open ⚠️
8 of the P0/P1 fixes are verified done. **The hub LCP is NOT resolved in production** (details below).

| Fix | Status |
|---|---|
| **Hub LCP** | ⚠️ **NOT resolved** — prod LCP still **7.6s** (Vercel, mobile PageSpeed). See below. |
| Fabricated `aggregateRating` | ✅ removed |
| Sitemap listing 5 redirecting categories | ✅ filtered to the 4 that return 200 |
| 2 `/use-case/` 404s | ✅ redirected |
| Homepage H1 "VerificationInspections" | ✅ clean SSR |
| Blog pagination (crawlable) | ✅ path-based `/blog/page/N/` |
| 4 industry H1s | ✅ keyword-led |
| 6 industries in nav + footer | ✅ |
| Page-specific OG images (top 10) | ✅ |

## 🔴 Hub LCP — still a launch blocker
Vercel production (mobile): **LCP 7.6s** · FCP 2.8s · Performance 66/100. Removing the `opacity:0` Framer wrapper helped FCP/text but **not** the LCP.

**Cause:** the LCP element is the hero image (`/images/industries/inspection-companies/hero.jpg`). It *is* preloaded (Next `priority`), but it's too heavy for throttled mobile (FCP 2.8s → LCP 7.6s gap).

**Fix:** optimize the hero image — lighter mobile variant (AVIF/WebP, lower quality, don't ship 1080w+ to phones), add `fetchpriority="high"` on the `<img>`. Re-test mobile LCP **< 2.5s**.

## Paid landing pages — your call on destinations
4 ad landing pages still **404** (GA4, last 12 months). Add redirects in `next.config.ts` — **you choose each destination** (you know the campaigns):

| Paid landing page | Paid sessions (last yr) | Redirect to → |
|---|---|---|
| `/landing-inspection-software-ads/` | 85 | _(your call)_ |
| `/landing-inspection-software-video/` | 47 | _(your call)_ |
| `/mold-inspection-remediation-software-landing/` | 37 | _(your call)_ |
| `/isn-vlx/` | 7 | _(your call)_ |

## Before launch
1. **Optimize the hero image** → hub LCP < 2.5s on mobile (the one real blocker left).
2. Add the 4 paid-LP redirects (destinations your call).
