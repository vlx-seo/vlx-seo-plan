# Post-Launch SEO Audit — vlx.ai

**Date:** 2026-07-22
**Site:** https://vlx.ai/ (Next.js 15 + Payload CMS — **now live in production**)
**Auditor:** Claude Code — live verification (Googlebot UA + curl) + code audit (`VLX-Marketing-master/`)
**Template applied:** `PROMPT-AUDIT-COMPLETO-VLX.md` v2.5 + `flujo-seo`
**Context:** The Next.js site **replaced WordPress**. There are **no more pre-launch "blockers"** — remaining items are reclassified as post-launch actions by urgency.

---

## 0. Status change — the migration is live

| Item | Before (pre-launch) | Now (verified live 2026-07-22) |
|---|---|---|
| Production `vlx.ai` | WordPress + NitroPack | **Next.js + Payload CMS** ✅ |
| Homepage meta robots | (WP) | `index, follow, max-image-preview:large, max-snippet:-1, max-video-preview:-1` ✅ |
| `robots.txt` | (WP) | Matches `src/app/robots.ts` (AI-crawler policy active) ✅ |
| `visualogyx.com` | old domain | **301 → vlx.ai in a single hop** (CloudFront) ✅ |
| `prod.vlx.ai` | indexable duplicate (risk) | **301 → app.visualogyx.com** (risk resolved) ✅ |
| `dev.vlx.ai` | Cognito | still behind Cognito (not indexable) ✅ |

**Overall verdict:** the launch was **clean on the fundamentals**: indexing correctly open, the old domain redirects in a single hop preserving equity, robots/sitemap/canonicals are coherent, schema is rich, and security headers are present. What remains are technical quality fixes (none blocking), listed below by priority.

---

## 1. Crawlability & Indexation

### ✅ Correct
- **robots.txt** (live): `Allow: /`; `Disallow: /lp/ /api/ /admin/` + 2 compare pages; advanced AI-crawler policy (allows OAI-SearchBot, PerplexityBot, Applebot, Claude-SearchBot/User; blocks GPTBot, ClaudeBot, Google-Extended, CCBot, Bytespider, Amazonbot, FacebookBot). `Sitemap: https://vlx.ai/sitemap.xml`.
- **sitemap.xml**: 200, **184 `<loc>`**. Valid `urlset`. Correctly excludes `/lp/*`, compare pages (phase 1), 9 consolidated integration pages (404), and the 3 blog categories that redirect (case-studies, checklists, product-updates). Uses a fixed date `2026-06-18` for static pages (avoids false freshness) and `updatedAt` for posts/case studies. **Good sitemap.**
- **`index,follow`** confirmed on homepage, hub, industry pages, and blog posts.
- **Compare pages** = `noindex, nofollow` ✅ (phase 1) + disallowed in robots.txt.
- **`trailingSlash: true`** globally → consistent canonical URL shape (all end with `/`).

### 🟠 Finding — "infinite" blog pagination / soft-404 (P1)
`/blog/page/N/` with N greater than the total number of pages **returns HTTP 200** instead of 404:
- `/blog/page/11/` → 200 · `/blog/page/50/` → 200 · `/blog/page/99/` → **200 with `<link rel="canonical" href="…/blog/page/99/">` (self-canonical)** and "no posts" content.
- The code (`blog/page/[page]/page.tsx:121`) has `if (pageNum > totalPages) notFound()`, but **the live deploy doesn't apply it** (build/deploy drift or edge caching serving 200).
- **Risk:** Google can crawl infinite empty pagination URLs, each self-canonical → *index bloat* and a soft-404 signal.
- **Contrast:** **category** pagination (`/blog/category/[slug]/page/N/`) does redirect (308) on overflow — inconsistent behavior between the two routes.
- **Fix:** ensure `/blog/page/N/` with N > total returns **404** (redeploy the branch that already has `notFound()`, or an edge rule). Verify with `curl -I /blog/page/99/`.

### 🟢 Blog pagination — what IS working
- **Crawlable path-based** architecture (B9 fix): page 1 at `/blog/`, the rest at `/blog/page/2/`…`/blog/page/10/` (10 pages, 12 posts/page).
- `/blog/page/1/` → **308 → `/blog/`** (correctly canonicalizes page 1) ✅.
- Pagination links are real `<Link href>`, **visible in the SSR HTML** (`Pagination.tsx`, `<nav aria-label="Blog pagination">`). Prev/Next + numbers with `aria-current`.
- Each paginated page declares its **own canonical** (`/blog/page/2/` → self) ✅ and appears in the sitemap.
- Post cards render as crawlable `<a href>` in the SSR fallback (`BlogListContent.tsx` → `BlogListFallback`).
- *Minor note:* `/blog/` uses `export const dynamic = 'force-dynamic'` (not SSG) — functionally OK (the SSR fallback is crawlable) but loses static caching; consider `revalidate` (ISR) instead of `force-dynamic`.

---

## 2. Content anchors (deep-linking with `#`)

**The specific request is mostly covered**, with one recommended improvement:

### ✅ Content headings carry `id` (`#` anchors)
- `RichTextRenderer.tsx` overrides the Lexical heading converter to emit an **`id` on every `h2`/`h3`** (unique slug via `uniqueSlugFor`, with `class="scroll-mt-24"` for the sticky-navbar offset).
- Verified live on `/blog/difference-qms-eqms/`: **25 of 30 `h2/h3` have an `id`** (the 5 without are chrome/nav headings, not content). Examples: `id="understanding-quality-management-in-modern-organizations"`, `id="key-differences-between-qms-and-eqms-software"`.
- This enables **deep-linking** (`…/post/#section`) and Google's "jump to" SERP links.

### 🟡 Improvement — the table of contents exposes no crawlable anchor links (P2)
- `BlogTOC.tsx` navigates with **`<button onClick={scrollTo}>` + IntersectionObserver**, not `<a href="#id">`. Live, the post has **only 1** `href="#…"`.
- Consequence: the `id`s exist (manual deep-linking works), but there are **no internal anchor links** for Google to use as a structure signal, and they don't work without JS.
- **Fix:** turn each TOC entry into `<a href="#${entry.id}">` (keep smooth-scroll via optional `preventDefault`). Low effort, improves a11y + potential "Jump to" sitelinks.

---

## 3. Semantic HTML & heading hierarchy

### 🟠 Site-wide broken heading hierarchy (P1) — CONFIRMED LIVE
The outline of **every** page starts with global-chrome headings **before** the `<h1>`:
- Homepage (live): `<h4>` … `<h4>` … `<h5>` … **then** `<h1>` "The AI-Native Enterprise Platform…".
- Hub (live): same `<h4><h4><h5>` pattern before the `<h1>` "Digital Inspections Software…".
- Origin (code): `Navbar.tsx` emits `<h4>By Use Case</h4>`, `<h4>By Industry</h4>`, `<h5>Run your inspection business on VLX</h5>` (mega-menu, present in SSR even when hidden by CSS); `Footer.tsx` emits `<p role="heading" aria-level={2}>`.
- **Impact:** incoherent heading structure site-wide + violates WCAG 1.3.1 / 2.4.6. Not fatal for indexing (the content `<h1>` exists and is unique), but it's a quality and accessibility signal.
- **Fix:** change the navbar `<h4>/<h5>` to `<p>/<span>` (same styling) and remove `role="heading" aria-level` from the footer. Target: outline = `<h1>` (hero) → `<h2>` section → `<h3>` subsection.

### ✅ Correct
- A single `<h1>` per page with a keyword (18/18 industry pages in the code audit; verified live on homepage/hub/post).
- Semantic `<article>`/`<time datetime>` in blog cards.

---

## 4. On-page & keyword strategy (strategic finding)

### 🟠 H1 ↔ title mismatch on the homepage (P1 strategic)
- **`<title>` / OG:** "Digital Inspection Software | VLX by Visualogyx" (primary keyword ✅, 47 chars ✅).
- **`<h1>` (live):** "**The AI-Native Enterprise Platform** for inspections, audits, and compliance".
- The H1 pivoted to a **brand-coined term with no search volume** ("AI-Native Enterprise Platform") while the title keeps the high-value keyword. The project's primary keyword ("digital inspection software") is **no longer in the H1** of the most important page.
- **Recommendation:** reintroduce the keyword into the H1 (or an immediate hero `<h2>`) without losing the "AI-Native" positioning. E.g. an H1 with "AI-Native Digital Inspection Software…" or a supporting H2 with the keyword. Reconcile with the keyword strategy (`AUDIT-KW-STRATEGY-2026-04-09.md`).

### ✅ Correct
- **Hub** `/digital-inspections-software/`: H1 with the keyword ("Digital Inspections Software for inspections, audits, and compliance") + **48 internal body links** to sub-pages → solid hub-cluster architecture (March Core Update penalty mitigated).
- Meta descriptions present with a value proposition and data ("12,000+ users, 25+ countries").
- Canonical slug `oil-gas` (redirects `oil-and-gas` → `oil-gas` via 308). ⚠️ *Strategic to-do:* the target keyword is "oil **and** gas"; reconcile slug vs strategy (the H1 does use "Oil and Gas").

---

## 5. Redirects & URL architecture

### ✅ Correct
- **`visualogyx.com` → 301 → vlx.ai** (single hop, CloudFront); old deep-links (`/digital-inspections-software/`) also 301. **Domain-migration equity flows correctly** → link recovery is on track.
- `www.vlx.ai` → 301 → `vlx.ai` (Cloudflare) ✅.
- Old slugs redirect: `/oil-and-gas/` → `/digital-inspections-software/oil-gas/`; `/real-estate/` resolved.

### 🟡 Findings
- **308 on Next.js internal redirects** (P2, low urgency): 297 rules with `permanent: true` in `next.config.ts` emit **308** (0 with 301). Confirmed live (`/oil-and-gas/` → 308, `/blog/page/1/` → 308). **Google has honored 308 as 301 since 2016**, so post-launch this is **low priority** (it was P0 pre-launch out of migration caution). Optional fix: emit 301 via middleware/edge if the canonical standard is desired.
- **`http://vlx.ai/` (apex) returns 200, not 301→https** (P2): HSTS + preload protects real browsers, but the apex http doesn't force a redirect (while `http://www.vlx.ai` does 301). Minor inconsistency; ideally 301 http→https on the apex too.
- `www.visualogyx.com` doesn't respond with a valid cert (possible dangling subdomain) — check whether any old backlink uses it.

---

## 6. Structured data (Schema)

### ✅ Very complete (live)
- **Homepage:** Organization, WebSite, SoftwareApplication + AggregateOffer, **FAQPage** (Question/Answer), VideoObject, ImageObject, ContactPoint, Place, PostalAddress.
- **Hub:** **CollectionPage + ItemList (ListItem)** ✅ — the "hub without ItemList" gap is **now resolved**. + BreadcrumbList, SoftwareApplication, Organization, WebPage.
- **Blog posts:** **Article** + BreadcrumbList + **Person** (author, E-E-A-T ✅) + Organization + WebSite.

### 🟡 Fine-tuning (P2)
- Posts use `Article` instead of **`BlogPosting`** (valid, but `BlogPosting` is the ideal type for a blog).
- The `/blog/` `CollectionPage.itemListElement` still uses **`.slice(0, 12)`** while declaring `numberOfItems: allPosts.length` → **count mismatch** (Google flags the discrepancy). Fix: remove the `.slice(0,12)` (doesn't affect HTML crawlability, only the JSON-LD).
- `AggregateOffer` with hardcoded `lowPrice 35 / highPrice 55` ignores annual plans (29/47) → misleading `lowPrice`.
- Duplicate `SoftwareApplication` without `@id` on integrations/kypit (not linked to the canonical node).

---

## 7. Performance, headers & social

### ✅ Correct (live)
- **Security headers:** `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: strict-origin-when-cross-origin`, **HSTS 2 years + preload**. `Cache-Control: s-maxage=31536000` (CDN cache for static) ✅.
- **OG/Twitter:** `og:title`, `og:description`, `og:image` **1200×629** absolute (`https://vlx.ai/opengraph-image…jpg`), `twitter:card=summary_large_image`, `twitter:site=@vlx_ai`. Correct `metadataBase` (absolute OG URLs to vlx.ai).

### 🟡 Adjustments
- **CSP in `Content-Security-Policy-Report-Only`** (documented decision) — move to *enforce* mode after stabilization (P2).
- `og:image` is 1200×**629** (should be 630 — trivial).
- **Core Web Vitals:** not yet measurable with field data (CrUX takes ~28 days post-launch). To do: PageSpeed on homepage/hub/blog/pricing as a lab baseline (P1 — see urgent actions). The WAF (503 under rapid crawling) prevented running it this session.

---

## 8. Analytics / Tracking
- The live CSP confirms the presence of **GA4 (googletagmanager/google-analytics), PostHog, Meta Pixel, Vercel** (allowlisted in `connect-src`/`script-src`).
- **Known risk (code, P2):** GA4 (`GoogleTag.tsx`) and Meta Pixel (`MetaPixel.tsx`) fire a pageview only on initial mount → **under-counting** in App Router SPA navigation. PostHog handles route changes. No GTM → no double-counting risk. Fix: add a route-change listener for gtag/Pixel.
- *(Live verification of IDs is pending due to the 503; confirm next session with spaced requests.)*

---

## 9. Sitemap sweep (184 URLs) — status of every URL

Full live sweep (Googlebot UA, 2s pacing after the WAF rate-limit):

| Metric | Value |
|---|---|
| Total URLs in sitemap | **184** |
| **Clean 200** | **176** |
| 308 redirects | **8** |
| 301 / 404 / 5xx | **0 / 0 / 0** |
| noindex (15 sample) | 0 |
| canonical pointing elsewhere (15 sample) | 0 |
| Sitemap URLs blocked by robots.txt | **0** |

**15-URL sample (homepage, hub, industries, posts, categories, blog+category pagination, integrations, pricing, product):** all `200`, `index,follow`, and **self-referencing canonical** — including `/blog/page/2/` and `/blog/category/industry-trends/page/2/`, which self-canonicalize correctly. No sitemap↔robots conflict (the sitemap doesn't include `/lp/`, `/api/`, `/admin/`, or `/compare/`).

### 🟡 Finding — 8 sitemap URLs are 308 redirects (P2)
A sitemap should not list URLs that redirect; their **destinations are already in the sitemap as 200** (signal duplication):

| Sitemap URL (308) | Redirects to (200, already in sitemap) |
|---|---|
| `/case-studies/empowers-qa-team-with-streamlined-inspection-processes/` | `/blog/empowers-qa-team-…/` |
| `/blog/product-updates-2026/`, `/blog/product-updates-2025/` | `/whats-new/` |
| `/blog/2020-2/` … `/blog/2024-2/` (5 URLs) | `/whats-new/` |

**Fix:** exclude these 8 from `sitemap.ts` (keep only the final 200 destinations).

### 🟡 Finding — possible duplicate content (P2, review)
Two near-identical slugs, both 200 and indexable:
`/blog/7-modern-inspection-methods/` **and** `/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/`. Check whether they are the same content → consolidate with canonical/301.

### ✅ Coverage by section (all 200 unless noted)
- **Core:** product, pricing, features, integrations (+zapier), demo, developers, whats-new.
- **Hub + 21 sub-pages** `/digital-inspections-software/*` (incl. app/countit, app/kypit, 16 industries, use-cases): 200.
- **Corporate/legal** (about, careers, contact, partners, faqs, security, accessibility, privacy, terms, eula): 200.
- **/press/** (4 releases) + **/case-studies/** (index + iti-international) 200; 1 case-study → 308.
- **Blog:** hub + pagination `/blog/page/2..10/` (9 pages, 200) + 4 categories with pagination (200) + ~55 posts (200); 7 "year/product-updates" posts → 308.
- **/checklists/** (hub + 35), **/use-case/** (10), **/forms/** (2), **/reports/** (1): 200.

> **Infrastructure note:** the 184-request sweep tripped **503 (ALB/WAF rate-limiting, `Server: awselb/2.0`)**, not an outage — every URL served 200/308 during the crawl. Real Googlebot spaces requests further apart; worth checking that the rate-limit rule doesn't throttle legitimate crawlers during spikes.

---

## 10. Actions — URGENT vs. NOT PRIORITY

> There are **no more blockers** (the site is live). Classified by real post-launch urgency.

### 🔴 TODAY / Launch week (verification & indexing)
1. **Submit sitemap to GSC** (`vlx.ai/sitemap.xml`) and **URLs via IndexNow** (Bing) — kick off recrawl of the new structure.
2. **Capture post-launch GSC/GA4 baseline** (clicks, impressions, position) to measure recovery.
3. **Monitor GSC Coverage daily** — watch for 404 spikes and how new URLs get indexed.
4. **PageSpeed/CWV baseline** on homepage, hub, top industry, blog, pricing (with spaced requests because of the WAF).
5. **Fix infinite pagination** `/blog/page/N/` (N>total) → must return **404**, not a self-canonical 200 (§1).

### 🟠 Week 1–4 (technical quality, high impact)
6. **Heading hierarchy**: remove navbar `<h4>/<h5>` and footer `role=heading` (§3).
7. **Homepage H1**: reintroduce the keyword "digital inspection software" into the hero H1/H2 (§4).
8. **Blog TOC** → anchor links `<a href="#id">` instead of `<button>` (§2).
9. **Blog schema**: remove `.slice(0,12)` from `itemListElement`; migrate `Article` → `BlogPosting` + `BreadcrumbList` (§6).
10. **Meta descriptions** missing/out of range (review inventory) with a CTA.
11. **Analytics under-count**: route-change listener for GA4 + Meta Pixel (§8).
12. **Clean sitemap**: exclude the 8 × 308 URLs (`sitemap.ts`) — keep only 200 destinations (§9).

### 🟡 Month 1–2 (optimization)
13. **308 → 301** on internal redirects (low urgency; Google honors 308) — via middleware/edge (§5).
14. `http://vlx.ai` (apex) → 301 https; check the dangling `www.visualogyx.com` (§5).
15. **CSP** Report-Only → enforce (§7). Fix `AggregateOffer` (include annual) and `@id` of SoftwareApplication (§6).
16. Reconcile slug `oil-gas` vs "oil-and-gas" with the keyword strategy (§4).
17. Review/consolidate the duplicate `7-modern-inspection-methods*` slugs (§9).
18. Review the ALB/WAF rate-limit rule (503 on spikes) — ensure it doesn't throttle legitimate crawlers (§9).
19. Striking-distance keywords (pos 5–25) — prioritize the hub and industry pages with fresh GSC data.

### ⚪ Ongoing
- Link recovery / outreach (~80 links to visualogyx.com — the 301 already preserves equity, reclaim the direct ones).
- Own listicle "Best Digital Inspection Software 2026" + outreach to wifitalents/gitnux/zipdo/xenia.
- AI mentions (target 80+/mo), ASO (App Store/Play), local GBP (Aventura FL).
- Field CWV (CrUX) monitoring at ~28 days.
- **HCU + forums/communities strategy (new, Phase 3 → HCU1/HCU2 in the dashboard):**
  - **HCU1 — Forum presence:** Google now surfaces community results heavily (Reddit, Quora "hidden gems") and AI Overviews cite them. Map and engage high-intent threads where "digital inspection software", "best inspection app" and industry pain points rank — Reddit (r/QualityAssurance, r/facilities, r/CommercialRealEstate, r/insurance, r/askengineers), Quora, niche EHS/inspection forums, G2/Capterra Q&A, LinkedIn groups. **Authentic, helpful first-hand** participation (disclose affiliation, add real value — no spam); seed answers where VLX is absent; track VLX presence in forum SERPs. Ties into AI mentions (S9/S13) and listicle outreach (S15).
  - **HCU2 — Experience-first content:** the Helpful Content system rewards real experience and helpfulness. Produce inspector/auditor POV pieces, anonymized own data, before/after and methodology, with **named authors + bios (E-E-A-T)**; repurpose that expertise into the forum answers and into `llms-full.txt` to also feed AI citations. Complements GEO (G2) and FAQ/AEO (G3).

---

## Appendix — Methodology
- Live verification with `Invoke-WebRequest`/`curl.exe` (Googlebot UA), no paid tools (seo-server unavailable this session → rankings/traffic/backlinks sections deferred to next session with GSC).
- Code audit over `VLX-Marketing-master/` (branch master, local checkout).
- ⚠️ Rapid crawling triggers the site's WAF (503 Service Temporarily Unavailable) → audit with spaced requests.
