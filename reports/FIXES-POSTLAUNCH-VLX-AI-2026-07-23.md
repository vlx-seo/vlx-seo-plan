# Post-launch audit fixes — vlx.ai

**Date:** 2026-07-23
**Source:** [AUDIT-POSTLAUNCH-VLX-AI-2026-07-22](https://github.com/vlx-seo/vlx-seo-plan/blob/main/reports/AUDIT-POSTLAUNCH-VLX-AI-2026-07-22.md)
**Branch / PR:** `seo/postlaunch-audit-fixes` → `master` (repo `Visualogyx/VLX-Marketing`)
**Commit:** `2c55d15` — 30 files
**Visual report (GitHub Pages):** https://vlx-seo.github.io/vlx-seo-plan/FIXES-POSTLAUNCH-VLX-AI-2026-07-23.html

---

## ✅ Fixed (included in the PR)

| # | Finding | Fix | Files |
|---|---------|-----|-------|
| **#2** | Broken heading hierarchy (WCAG 1.3.1 / 2.4.6) | Navbar `<h4>/<h5>` → `<p>` (same classes); `role="heading" aria-level={2}` removed from the Footer | `Navbar.tsx`, `Footer.tsx` |
| **#4** | Blog TOC not crawlable | `<button onClick>` → `<a href="#id">` (keeps smooth scroll + hash; enables "Jump to" sitelinks; works without JS) | `BlogTOC.tsx` |
| **#5** | Sitemap listed 308-redirecting URLs | Exclude `product-updates` posts (→ /whats-new/) and duplicate case studies that redirect to /blog/ | `sitemap.ts` |
| **#9** | `CollectionPage.numberOfItems` mismatch | `numberOfItems` now derives from the same 12-item slice as the list | `blog/page.tsx` |
| **#10** | `AggregateOffer` price range incomplete | `lowPrice` 35 → 29 (annual Pro plan); highPrice stays 55 | `schema.ts` |
| **#11** | `SoftwareApplication` nodes missing `@id` | Unique `@id` on KYPiT/COUNTiT/integration nodes, linked to `#software` / `#organization` | `kypit/page.tsx`, `countit/page.tsx`, `integrations/[slug]/page.tsx` |
| **#12** | Blog used generic `Article` | `Article` → `BlogPosting` | `blog/[slug]/page.tsx` |
| **#13** | GA4 + Meta Pixel missed SPA navigations | Fire a pageview on each route change via `usePathname()` (skips the initial mount to avoid double-counting) | `GoogleTag.tsx`, `MetaPixel.tsx` |
| **#15** | OG image 1200×629 | Resized to 1200×630 (OG + Twitter, root and (frontend)) | 4× `opengraph-image.jpg` / `twitter-image.jpg` |
| **#16** | Slug `oil-gas` ≠ keyword "oil and gas" | Route renamed to `/oil-and-gas/` + 301 from the old slug; internal links and slugs updated | `next.config.ts`, `oil-and-gas/` route, +11 link files |

### Note on #3 (homepage H1)
Implemented and then **reverted by request**. The H1 is back to *"The AI-Native Enterprise Platform for…"* with the original typewriter rotation. **Not part of the PR.** The keyword remains present in the page `<title>`.

---

## ⏳ Pending

### Infrastructure (not app code — needs AWS/DNS access)
| # | Issue | Action |
|---|-------|--------|
| **#1** | `/blog/page/N/` beyond the last page returns **200** instead of 404 | The code already calls `notFound()`; this is a **CDN/edge cache** lag. Redeploy + invalidate CloudFront, verify with `curl -I https://vlx.ai/blog/page/99/` |
| **#7** | `http://vlx.ai` does not force HTTPS (200 instead of 301) | Add a 301 at the edge/ALB |
| **#8** | `www.visualogyx.com` has no valid SSL (dangling subdomain) | Check for old backlinks and configure a 301 → vlx.ai |
| **#14** | CSP is in `Report-Only` | Switch to enforce mode after stabilization (week 2–4) |
| **#17** | WAF/ALB may 503 legitimate crawlers under fast crawls | Review the rate-limit threshold so Googlebot isn't throttled |

### Content / decisions
| # | Issue | Action |
|---|-------|--------|
| **#6** | Near-duplicate slugs `7-modern-inspection-methods` vs `…-that-boost-efficiency-save-costs` | Two **distinct posts, both with traffic**. Review in Payload whether the content is duplicated. If so → 301 the weaker into the stronger |
| **#18** | Meta descriptions out of range | ~14 pages at 161–210 chars (none missing). Trim to ≤160 with a CTA (copy decision) |

### Deploy steps (after the PR is approved)
1. Merge the PR to `master` → auto-deploy to AWS ECS (prod).
2. **Invalidate CloudFront** (`aws cloudfront create-invalidation --distribution-id <ID> --paths "/*"`) — without this the CDN keeps serving old HTML.
3. Verify: `curl -I https://vlx.ai/digital-inspections-software/oil-gas/` → **301** to `/oil-and-gas/`.
4. Resubmit `sitemap.xml` in GSC + IndexNow; validate schemas in the Rich Results Test; refresh the OG image in the Facebook Sharing Debugger.

---

## 🖥️ New observation — Homepage H1 typewriter looks "incomplete" in screenshots

**Behavior:** The hero H1 uses a letter-by-letter typewriter animation that rotates words (Inspections/Compliance/Audits…) with a blinking cursor (`animate-blink`). Any static capture — Google Search Console, screenshot tools, social/OG share previews — can freeze the H1 mid-word, giving the false impression that the page didn't fully load or that the text is cut off.

**Real impact:** Cosmetic / perception only. **No indexing impact** — the server-rendered HTML already contains the full word. Minor risk in social/OG previews and in screenshot-based QA reviews.

**Root cause:** Animated text component (typewriter with `animate-blink`) in the H1, with no guaranteed static "complete" state at the instant of capture. See `HeroTypewriter.tsx`.

**Recommended fixes (priority order):**
1. **Honor `prefers-reduced-motion`** — render the full word static (no animation) for users/bots that request it; this also covers many crawlers and capture tools.
2. **Guarantee a "resting state"** where the rotating word is complete (never mid-word) and the blinking cursor is not part of the meaningful text, so any capture shows a whole word.
3. **Investigate main-thread saturation** — screenshots/timers froze during the audit; look for long-running scripts / costly animations and optimize to avoid render blocking.
4. **Rely on the static OG image** (already 1200×630, see #15) for social previews instead of depending on an animated render.

**Status:** Pending — newly identified, tracked as **PL6** in the [plan dashboard](https://vlx-seo.github.io/vlx-seo-plan/vlx-postlaunch-plan-2026-07-22.html). Not part of the current code PR.

---

## 🛠️ Proposed task — In-site cache-management tool

**Goal:** let editors purge/invalidate the CloudFront cache (and see cache state) from the site's own admin, without relying on the AWS CLI or requiring personal AWS credentials.

**Motivation:** today, every deploy or content fix stays invisible until CloudFront is invalidated by hand on the command line. This creates friction and makes "already-deployed fixes" look unapplied (see the cache analysis below — it's the root cause of #1 and of fixes not showing without an invalidation).

**Suggested scope (MVP):**
- Auth-protected action in the Payload admin to trigger a CloudFront invalidation:
  - Full purge (`/*`) or by specific paths (e.g. only `/blog/*`).
  - Ideally, a "purge this page" button in each post/page editor.
- Show the status of the last invalidation (pending/complete) and the current cache headers of a URL.

**Technical / security considerations:**
- Use the AWS SDK with a **least-privilege IAM role** (only `cloudfront:CreateInvalidation` / `GetInvalidation` on the specific distribution). Never expose credentials client-side.
- **Rate-limit**: invalidations bill after the free tier (1,000/month); avoid looped full purges.
- Log who triggers each purge (audit trail).
- While at it, review the redirect policy: `permanent: true` (301) is cached aggressively; consider `302` for rules that may change, to avoid poisoning browser caches.

**Priority:** medium. Doesn't block launch, but removes recurring operational friction and reduces the "fixed but not visible" risk.

---

## 📌 Why is caching affecting the site?

The site has **two caching layers** that explain the symptoms observed:

### 1. CDN (CloudFront) — `Cache-Control: s-maxage=31536000`
Pages are served with a CDN cache of **up to 1 year**. When a code change is deployed, **CloudFront keeps serving the old HTML** until an explicit invalidation. Consequences:
- Already-deployed fixes "don't show" until invalidated.
- It's the cause of **#1**: the pagination code already returns `notFound()` for out-of-range pages, but the edge serves a cached 200.

### 2. Browser — `permanent: true` redirects (HTTP 301)
Browsers **cache 301s indefinitely**. If a pagination redirect rule existed at some point (documented at `next.config.ts:905`, there was a `?page=` version that broke pagination), any browser that hit it **cached that 301 forever**. As a result, that browser keeps redirecting `/blog/page/N/` → `/blog/` locally, **even though the server is now correct**.
- This is exactly what was observed: in a clean/incognito browser, pagination works (200, no redirect); in the browser with the cached 301, "every page redirects to the main one".
- There's no easy way to "un-cache" a stored 301 from the server side: the user must clear cache, or wait for it to expire. Hence the value of using `302` for potentially temporary rules.

**Summary:** caching doesn't "break" the site, but it desyncs what the user sees from the server's real state — which is why an accessible invalidation tool and a more careful redirect policy are worthwhile.
