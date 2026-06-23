# VLX Pre-Migration Fixes — Verification (2026-06-23)

**By:** Laura Ceballos / Software Craft CR · **Scope:** `origin/dev` (Visualogyx/VLX-Marketing) · From the 2026-06-19 pre-migration SEO audit.

## Verdict: 9/10 resolved — hub LCP still open ⚠️
8 of the P0/P1 fixes are verified done. **The hub LCP is NOT resolved in production** (details below).

| Fix | Status |
|---|---|
| **Hub LCP** | ⚠️ **NOT resolved** — prod LCP still **~6–7.6s** mobile (Vercel). Root cause corrected below: it's the FCP / render path, **not** image weight. |
| Fabricated `aggregateRating` | ✅ removed |
| Sitemap listing 5 redirecting categories | ✅ filtered to the 4 that return 200 |
| 2 `/use-case/` 404s | ✅ redirected |
| Homepage H1 "VerificationInspections" | ✅ clean SSR |
| Blog pagination (crawlable) | ✅ path-based `/blog/page/N/` |
| 4 industry H1s | ✅ keyword-led |
| 6 industries in nav + footer | ✅ |
| Page-specific OG images (top 10) | ✅ |

## 🔴 Hub LCP — still a launch blocker (root cause corrected)
Vercel production, mobile: **LCP ~6–7.6s** (target is 2.5s) · FCP ~2.8s · Performance 66/100. Confirmed with both PageSpeed Insights and a throttled field measurement (Slow-4G + 4× CPU). Removing the `opacity:0` Framer wrapper helped the text/FCP but **did not** fix the LCP.

**LCP element = the hero image** (`/images/industries/inspection-companies/hero.jpg`) — confirmed via PerformanceObserver, not inferred.

**What is NOT the bottleneck (don't spend time here):**
- **Image weight / format.** The mobile variant Next serves is only **~24 KB** (`w=1080&q=75`) and downloads in ~1.5s. It is **already preloaded** (`<link rel="preload" as="image">`) and already has **`fetchPriority="high"`**. Compressing it further or switching to AVIF will **not** move LCP. (This corrects the earlier note — apologies, the first pass assumed image weight.)
- **TTFB / server.** ~100 ms, served from Vercel edge cache (`x-vercel-cache: HIT`). The server is fast.
- **Render-blocking bytes.** HTML is ~30 KB brotli, the 2 blocking CSS files ~17 KB brotli total — small.

**The real cause: LCP is gated by a slow First Contentful Paint (~2.8s).** The page paints nothing until ~3–4.5s; the (light, preloaded) hero then paints ~1.5s after. LCP can't beat FCP, so **the lever is FCP, not the image.** With fast TTFB and small transfer, the FCP cost is **main-thread parse/render under mobile CPU throttle**, whose likely contributors are:
- a **~240 KB (uncompressed) SSR DOM** — a lot of below-the-fold content rendered up front;
- a CSS sheet that is small over the wire but **decompresses large to parse**;
- **~21 JS chunks** hydrating on the main thread.

**Fix (FCP levers — verify with Lighthouse "Reduce unused CSS/JS" + "Minimize main-thread work"):**
1. **Shrink what the browser must parse before first paint** — defer/lazy-render the hub's below-the-fold sections and purge unused CSS so the initial DOM + critical CSS are smaller. This is the main lever.
2. Keep the hero preload + `fetchPriority` (already correct) — leave the image alone.
3. Re-test mobile LCP **< 2.5s** in Lighthouse after each change, and watch FCP as the leading indicator.

> ⚠️ **Validating the LCP can only happen on Vercel, once the changes are deployed and the page is navigable.** An LCP fix can't be confirmed until it's live on the deploy. The hero fade fix (`animate-hero-fade-in`) **is already on the current preview** and LCP is still ~6–7.6s — that's the evidence the FCP work above is the real lever. So: do the FCP work, **deploy the full latest dev to Vercel**, confirm the hub is navigable, then re-run Lighthouse mobile to confirm LCP < 2.5s. (Heads-up: the current preview is also missing the sitemap / use-case / H1 fixes — deploy the full dev for a clean, representative validation.)

## Paid landing pages — your call on destinations
4 ad landing pages still **404** (GA4, last 12 months). Add redirects in `next.config.ts` — **you choose each destination** (you know the campaigns):

| Paid landing page | Paid sessions (last yr) | Redirect to → |
|---|---|---|
| `/landing-inspection-software-ads/` | 85 | _(your call)_ |
| `/landing-inspection-software-video/` | 47 | _(your call)_ |
| `/mold-inspection-remediation-software-landing/` | 37 | _(your call)_ |
| `/isn-vlx/` | 7 | _(your call)_ |

## Before launch
1. **Bring the hub's FCP down** (defer below-the-fold DOM, purge unused CSS) → hub LCP < 2.5s on mobile. The one real blocker left — and the hero image is **already** optimized, so don't touch it.
2. Add the 4 paid-LP redirects (destinations your call).
