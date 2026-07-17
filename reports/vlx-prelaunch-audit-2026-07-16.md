# Technical SEO Audit — New VLX site (Next.js) · Pre-launch
**Date:** 2026-07-16 · **Auditor:** Laura Ceballos / Software Craft CR
**Object:** New Next.js site (repo `Visualogyx/VLX-Marketing`, Hans-review branch). **The team confirmed `dev.vlx.ai` and `prod.vlx.ai` serve the same build**, so the live verification was done on **`dev.vlx.ai`** (accessible) and applies equally to `prod.vlx.ai`. Both environments sit behind **AWS ALB + Amazon Cognito**.
**Base template:** `PROMPT-AUDIT-COMPLETO-VLX.md` v2.4 — reviewed in full, no sections skipped.
**Priorities:** (1) **load speed**, (2) **anything that blocks the production launch**. The rest of the template is covered too.

> ⚙️ **Living document.** Data captured live on 2026-07-16 via an authenticated in-browser `fetch()` crawler over the 184 sitemap URLs + targeted probes. The server showed intermittent outages during the session (see §6).

---

## ⚠️ Methodology notes
1. **Access:** environments behind Cognito → only auditable from an authenticated browser. `prod.vlx.ai` blocked automated access, so the audit ran on `dev.vlx.ai` (build confirmed identical to prod by the team).
2. **External tools blocked by Cognito:** PageSpeed, Lighthouse, CrUX and public crawlers cannot reach the site → performance measured with in-browser Performance APIs (lab, not field). **Real field LCP/INP can only be measured with PageSpeed once Cognito is removed (post-launch).**
3. **Backlink/keyword tooling unavailable this session** → backlinks/keywords/SERP/ASO are marked **PROVISIONAL** or taken from the last verified project data.
4. **FCP/LCP limitation:** the automation tab stays in the background (`visibility:hidden`), which throttles rendering and **skews FCP/LCP**. Load speed is assessed with throttle-independent metrics (resource weights via `decodedBodySize`, TTFB, DOM size, CLS) + historical context.
5. **GSC/GA4:** the new site is not a verified property → no traffic baseline (expected pre-launch).
6. **Indexing rule (client, 2026-07-16):** pre-launch environments must serve `noindex, nofollow`. Finding `index, follow` in pre-launch is a **flag**.

---

## 🚦 LAUNCH VERDICT

**Is there anything that blocks the production launch? → YES. 2 P0 blockers and 1 infrastructure risk must be resolved before go-live.**

| # | P0 blocker | Why it blocks launch |
|---|---|---|
| **P0-1** | **Indexing is hard-coded per page, not per environment.** 182/184 pages serve `index, follow` (should be `noindex,nofollow` in pre-launch) **and** `/blog/` + `/contact/` serve a fixed `noindex, nofollow` → **those two will stay noindex in production**, de-indexing the blog index and cutting `nofollow` link flow to posts. | Breaks indexing both ways: risk of accidental indexing if Cognito drops, and a de-indexed blog at launch. |
| **P0-2** | **Paid landing pages returning 404.** 5 of 7 known campaign LPs return 404 (`capterra-a`, `linkedin`, `video`, `mold-remediation`, `isn-vlx`) + all `/lp/*`. Only `g2` and `ads` resolve. 404s with historical paid traffic (video 47, mold 37, isn-vlx 7 sessions/yr). | With active Capterra/G2/LinkedIn/Ads campaigns, the paid click lands on a 404 = wasted spend. **Confirm active campaigns with Hans.** |
| **⚠️ Infra risk** | **Server instability.** Intermittent outages and hub TTFB swinging between ~85 ms and **910 ms** during the audit. | If it reflects prod infra, it's an availability and LCP risk at launch. Validate with the infra team. |

**High impact (non-blocking, week 1):** missing OG images on 12 industry pages (P1), hub load speed / heavy JS (P1), 8 redirecting URLs in the sitemap (P2), titles/metas out of range (P2).

---

## 🧭 Progress tracker

| # | Section | Status |
|---|---|---|
| 1 | Rendering / SSR | ✅ |
| 2 | Sitemap | ✅ |
| 3 | Schema (JSON-LD) | ✅ |
| 4 | H1s | ✅ |
| 5 | noindex / indexability | ✅ |
| 6 | Core Web Vitals / load speed | ✅ (lab; field pending post-launch) |
| 7 | GSC analysis | ⬜ PROV (n/a pre-launch) |
| 8 | GSC anomalies | ⬜ PROV |
| 9 | Analytics & tracking | 🟨 (gtag present; verify at launch) |
| 10 | Server & HTTP headers | ✅ |
| 11 | Social tags (OG/Twitter) | ✅ |
| 12 | URL normalization / redirects | ✅ |
| 13 | Content audit | ✅ |
| 14 | Competitor benchmark | ⬜ PROV |
| 15 | Backlinks / link recovery | ⬜ PROV |
| 16 | AI / GEO | ✅ |
| 17 | Pre-launch blockers (B1–B8) | ✅ |
| 18 | Confirmed critical issues (C1–C4) | ✅ |
| 19 | Strategy docs review | ✅ |
| 20 | Launch verdict | ✅ |
| 21 | Post-launch checklist | ✅ |
| 22 | Keyword walkthrough (on-page per URL) | ✅ |
| 23 | Correction prompts for the dev | ✅ |
| 24 | Action plan — additional improvements (non-blocking) | ✅ |

---

## Golden rules applied
- Live data, today (2026-07-16). Prior reports = historical context, not current reality.
- Context: visualogyx.com→vlx.ai domain migration (Sep-2025, authority still transferring); WP→Next.js platform migration **not launched** (this new build is the pre-launch candidate).
- Distinguish the new build (dev/prod.vlx.ai) from vlx.ai (WordPress in real production).

---

# Findings by section

## 1. Rendering / SSR — ✅ OK
- **184/184 sitemap URLs return 200** with complete HTML (H1, title, meta, schema, links) via raw `fetch()` = exactly what Googlebot sees. **SSR works site-wide**; no JS required for main content.

## 2. Sitemap — 🟡 (hygiene, P2)
- 184 URLs, single sitemap, `application/xml`. All canonicalized to `vlx.ai` (correct for the cutover).
- **8 sitemap URLs REDIRECT** (should not be listed; sitemaps should only contain final 200 URLs):
  - `/case-studies/empowers-qa-team-.../` → `/blog/empowers-qa-team-.../`
  - `/blog/product-updates-2026/`, `/product-updates-2025/`, `/2020-2/` … `/2024-2/` (7) → `/whats-new/`
  - **Fix:** remove them from the sitemap.

## 3. Schema (JSON-LD) — ✅ (very complete; minor gaps P3)
| Page | # | Schemas |
|---|---|---|
| Home | 1 | Organization, WebSite, SoftwareApplication, FAQPage, VideoObject |
| Hub | 1 | Organization, WebSite, **CollectionPage**, SoftwareApplication, BreadcrumbList |
| Industries | 18 | Organization, WebSite, **Service**, **FAQPage**, BreadcrumbList |
| Apps | 2 | Organization, WebSite, SoftwareApplication, BreadcrumbList |
| Blog posts | 68 | Article (61 real), Organization, WebSite, BreadcrumbList |
| Checklists / Use-case | 46 | Article, BreadcrumbList, Organization, WebSite |

- **Do NOT report schemas as missing** (the template warns about this): the hub already has CollectionPage; Organization/SoftwareApplication/FAQ are implemented.
- The "7 blog posts without Article" = the 7 redirecting stubs (product-updates / 20XX-2) → not real posts. Real posts have Article. ✅
- **Minor gap (P3):** compare pages unpublished (product decision) — no comparison schema, not an error.

## 4. H1s — ✅ (1 exception)
- **Home H1:** "The AI-Native Enterprise Platform for [Inspections/Compliance/…]" (typewriter). Clean SSR, no "VerificationInspections" concatenation bug. ✅
- **12/12 industries have keyword-led H1s.** ✅
- **🟡 `/blog/` has 2 H1 tags** (should be 1).

## 5. noindex / indexability — 🔴 **P0-1 (blocker)**
- **182/184 pages = `index, follow`**; only `/contact/` and `/blog/` = `noindex, nofollow`.
- **The noindex is NOT environment-based** (no env logic) — it's fixed per page. Consequences:
  1. **Pre-launch (client rule):** the 182 pages should be `noindex,nofollow` and aren't → violation. Partial mitigation: canonical→`vlx.ai` + Cognito blocks crawlers today, but if Cognito is removed or a URL leaks, it can be indexed as dev/prod.
  2. **At launch:** `/blog/` and `/contact/` will remain `noindex,nofollow` → **the blog index would be de-indexed** and `nofollow`. `/contact/` noindex is usually acceptable, but `/blog/` is not.
- **Current state is safe:** Cognito blocks search engines, so nothing is being indexed right now — no live SEO damage. The fix is about getting the config right for launch.
- **Fix (before go-live):** environment-based robots (all pre-prod `noindex,nofollow`; production `index,follow`) and ensure `/blog/` is indexable in production. See correction prompt #1.

## 6. Core Web Vitals / LOAD SPEED — 🟠 P1 (priority focus)
| Metric (hub) | Value | Reading |
|---|---|---|
| TTFB | 85 ms (warm) … **910 ms** (cold) | Variable → server instability |
| SSR HTML | 141 KB / 20 KB gzip (hub) · 352 KB / 43 KB (home) | Home heavy (1,776 DOM nodes) |
| **JavaScript** | **893 KB–1.46 MB / 274–445 KB gzip · 29–69 files** | **Main FCP/LCP bottleneck** |
| CSS | 130–260 KB / 20–40 KB gzip | High |
| Images | ~24 KB (lightweight hero) | ✅ Not the problem |
| CLS | 0 | ✅ |
| FCP / LCP | Not reliably measurable in automation (background tab skews paint) | Measure with PageSpeed post-Cognito |

- **Diagnosis:** infrastructure is fast when warm (TTFB ~85 ms, CLS 0, optimized images); the bottleneck is **heavy JS + large SSR DOM**, which delays FCP → LCP under real mobile CPU throttling.
- **History:** last real throttled measurement (PageSpeed, June, hub) = **LCP ≈ 7.6 s**, diagnosed as FCP/render-path limited — not the image. Current weights confirm the diagnosis persists.
- **Fix (P1):** code-split and defer below-the-fold JS, purge unused JS/CSS, lighten the home; re-measure mobile LCP `<2.5s` with PageSpeed once public. See correction prompt #3.

## 7–8. GSC / GSC anomalies — ⬜ PROVISIONAL
- The new site is not a verified GSC property → no traffic/indexing baseline (expected pre-launch). Populates at launch; see post-launch checklist §E.

## 9. Analytics & tracking — 🟨 (verify at launch)
- `gtag/js` **present** (GA4 via gtag); GTM not detected. GA4 ID not cleanly extractable (likely client-side injected). **Verify at launch:** single GA4, no double-counting, conversion events (Start Free Trial / Book a Demo) firing. See §21-D.

## 10. Server & HTTP headers — ✅ (1 improvement P3)
- `Strict-Transport-Security` ✅ · `X-Frame-Options: DENY` ✅ · `X-Content-Type-Options: nosniff` ✅ · `Referrer-Policy: strict-origin-when-cross-origin` ✅ · `Cache-Control: s-maxage=31536000` ✅.
- **Content-Encoding: gzip** — 🟡 P3: Brotli preferable for text. **No CSP header observed** — P3.

## 11. Social tags (OG / Twitter) — 🟠 P1/P2
- **46/184 pages WITHOUT `og:image`**, including **12 industry pages** (high commercial intent), apps, integrations, demo, whats-new, checklists, security, developers, faqs, careers, partners, contact, press (all), case-studies, blog categories, and 7 stubs.
- **Fix (P1 for industries, P2 rest):** page-specific `og:image` ≥1200×630, prioritizing the 12 industries. Twitter card `summary_large_image` present where OG exists. See correction prompt #4.

## 12. URL normalization / redirects — ✅ (mostly resolved)
- **Trailing slash:** `/checklists` → `/checklists/` (301). Consistent. ✅
- **Old WP slugs:** `/oil-and-gas/` → `/digital-inspections-software/oil-gas/` ✅ · `/financial-institutions/` → `/digital-inspections-software/financial-services/` ✅ (were 404 on July 1 — **resolved**).
- **🟡 Top-level `/oil-gas/` → 404** (P3; add a redirect if it ever had links).
- **Blog pagination:** `/blog/page/2/` = 200, `index,follow`, self-canonical ✅ (**resolved**). 🟡 `/blog/?page=2` also 200 (verify the canonical consolidates it; P3).
- **301 vs 308 (B1):** `permanent:true` in `next.config.ts` emits **308**. Google treats 308 = 301 for SEO; **not a blocker**, optional to switch to 301 (P3).

## 13. Content audit — 🟡 P2
- **31 titles** out of range (hub 71, several industries 64–67, press 78–103, blog pagination 68–69).
- **32 meta descriptions** >160 — notably `/use-case/the-essential-hotel-room-housekeeping-checklist/` at **1,177 chars**, press releases 272–352, 10 industries 167–210. Cleanup pass recommended.
- **No critical thin/orphan content** (SSR + internal links OK; hub links to its 19 children).

## 14–15. Competitors / Backlinks — ⬜ PROVISIONAL
- No backlink/keyword tooling this session. Reference (last audit): competitors SafetyCulture, CompanyCam, GoFormz, Joyfill, Donesafe, Certainty, Fulcrum. Post-migration link recovery (~80 links to visualogyx.com unclaimed). VLX absent from top listicles (wifitalents/gitnux/zipdo/xenia). No code-dependent changes today.

## 16. AI / GEO — ✅
- **robots.txt** keeps the deliberate AI posture: `Allow` for OAI-SearchBot, PerplexityBot, Applebot, Claude-SearchBot, Claude-User; `Disallow` for CCBot, Bytespider, Google-Extended, GPTBot, Applebot-Extended, ClaudeBot, Amazonbot, FacebookBot.
- `Sitemap: https://vlx.ai/sitemap.xml` ✅. `llms.txt` present (5.65 KB). 
- Note: the pre-prod robots.txt has `Allow: /` → reinforces the need for environment-based noindex (P0-1).

## 17. Pre-launch blockers (B1–B8)
| # | Historical blocker | Status today |
|---|---|---|
| B1 | 308 vs 301 | 🟡 P3 (308 accepted by Google) |
| B2 | Blog SSR | ✅ Resolved |
| B3 | Paid landing pages | 🔴 **P0-2 — 5/7 in 404** |
| B4 | Category pagination | ✅ |
| B5 | `/oil-gas/` redirect | 🟡 P3 top-level 404; canonical OK |
| B6 | `/checklists/` | ✅ |
| B7 | BlogFilterBar crawlable | ✅ |
| B8 | `/real-estate` | ✅ |

## 18–19. Critical issues & strategy docs — ✅ reviewed
- June-19 technical must-fixes mostly resolved; the real pending items match: **paid landers (P0-2)** and **hub performance (P1)**. Added now: **environment-based indexing (P0-1)**, not caught before because the template assumed an auto-noindex that this build doesn't have.

---

## 📋 21. Post-launch validation checklist
> Run in order as soon as Cognito is removed and the site is public on `vlx.ai`.

### A. Indexing & crawling (first hours)
- [ ] `meta robots` = **index, follow** on all public pages (opposite of pre-launch).
- [ ] Confirm `/blog/` and `/contact/` are now **index,follow** (pre-prod noindex did not propagate).
- [ ] `robots.txt` correct; AI posture intact; `Sitemap:` → `https://vlx.ai/sitemap.xml`.
- [ ] `sitemap.xml` 200, no redirecting URLs (clean up the 8) or 404s.
- [ ] Canonicals → `https://vlx.ai/…`. Submit sitemap in GSC + request indexing of home and hub. Submit key URLs via IndexNow/Bing.

### B. Redirects & migration (day 0)
- [ ] Every vlx.ai (WordPress) URL with traffic/links resolves or redirects in 1 hop, no 404s.
- [ ] Active paid landing pages resolve 200 (not 404).
- [ ] Redirects 301 (or 308), no A→B→C chains. `visualogyx.com` still redirects to `vlx.ai`.

### C. Real performance (day 0–1, Cognito removed)
- [ ] PageSpeed mobile on home, hub and top industry → record LCP/INP/CLS/FCP/TTFB.
- [ ] Confirm hub LCP < 2.5 s (historical ~7.6 s; focus on reducing JS/FCP).
- [ ] Check INP with the real bundle; confirm TTFB stable (no ~900 ms spikes).

### D. Analytics & tracking (day 0)
- [ ] GA4 recording pageviews once per navigation (no GTM + direct double-counting).
- [ ] Conversion events (Start Free Trial / Book a Demo) firing. GSC ↔ GA4 linked.

### E. Data & monitoring (week 1)
- [ ] GSC coverage with no mass errors; sitemap "success".
- [ ] Export GSC/GA4 baseline to measure migration impact.
- [ ] Field Core Web Vitals (CrUX) populate ~28 days post-launch.
- [ ] Monitor rankings and organic traffic vs the WordPress baseline.

### F. Schema & social (week 1)
- [ ] Validate rich results (Organization, SoftwareApplication, FAQ, Article, Breadcrumb) in GSC.
- [ ] `og:image` ≥1200×630 on the 12 industries + key pages; validate in Meta/X/LinkedIn.

---

## 22. Keyword walkthrough (on-page, per URL) — 🟡 P2 (non-blocking)

On-page walkthrough of the priority pages against the keyword-URL mapping (`KW-URL-MAPPING-2026-04-01-EN.md`): is each page's target keyword present in title/H1/meta? Verified live 2026-07-16.

**Verdict:** on-page targeting is **strong** — a big improvement over the April mapping (which flagged lost keywords in several industries). **11/12 industries have keyword-led H1s.** The items are P2/P3 refinements — **none block launch**.

| Page | Target keyword | Title | H1 | Verdict |
|---|---|---|---|---|
| `/` (home) | digital inspections software | 🟡 "Digital Inspection Software" (singular) | ❌ brand-led ("AI-Native Enterprise Platform") | H1 not keyword-led — brand choice; reinforce KW in title |
| `/digital-inspections-software/` (hub) | digital inspections software | ✅ | ✅ | Perfect |
| `…/oil-gas/` | oil and gas inspection software | 🟡 "…for Oil & Gas" | ✅ | H1 recovered KW; align title |
| `…/vehicle/` | digital vehicle inspection software | ✅ | ✅ | Perfect |
| `…/real-estate/` | property inspection software for real estate | ❌ | ✅ | Align title to the H1 keyword |
| `…/insurance/` | insurance claims inspection software | 🟡 | ✅ | Good |
| `…/financial-services/` | virtual field inspection sw. for financial institutions | 🟡 | ✅ | H1 nails the mapping KW; "services" vs "institutions" in URL |
| `…/manufacturing/` | digital inspection software for manufacturing | ✅ | ✅ | Perfect |
| `…/transportation-logistics/` | transportation inspection software | 🟡 | 🟡 | Low-volume KW; acceptable |
| `…/quality/` | digital quality inspection software | ❌ | ✅ | Align title to the H1 keyword |
| `…/virtual/` | virtual inspection software | ✅ | ✅ | Good |
| `…/mining/` | mining inspection software | ✅ | ✅ | Perfect |
| `…/hospitality/` | hotel inspection software | ❌ | ❌ | **Gap:** targets "hospitality", not "hotel" (where the volume is) |
| `…/construction/` | construction inspection software | 🟡 | ✅ | Good |
| `…/safety/` | safety inspection software | ✅ | ✅ | Good |
| `…/home-inspectors/` | home inspection software | ✅ | ❌ | Title has KW; make H1 keyword-led |
| `…/service-companies/` | service company inspection software | ✅ | 🟡 | Good |
| `…/asset-verification/` | asset verification software | ✅ | 🟡 | Good |
| `…/field-operations/` | field operations inspection software | ✅ | 🟡 | Good |
| `…/inspection-companies/` | inspection software for inspection companies | ✅ | 🟡 | H1 generic; could target "inspection companies" |
| `…/app/countit/` | AI object counting app | ✅ | ❌ | H1 "Snap, Count & Share" not keyword-led (had 3,002 GSC impr, pos 12.6) |
| `…/app/kypit/` | KYPiT fraud detection | ✅ | ✅ | Brand + feature — OK |

**Walkthrough findings**
1. **H1 not keyword-led on 3 pages** (P2): home (brand-led, deliberate), home-inspectors, countit. Home is a brand call; home-inspectors and countit already have the keyword in the title → easy to align the H1.
2. **Title↔H1 misalignment** (P2): real-estate and quality have the right keyword in the H1 but the title opens with "Digital Inspection Software for…" instead of the specific KW. Align the titles.
3. **Hospitality keyword gap** (P2): the page targets "hospitality", not "hotel" — where the volume is (hotel maintenance/inspection).
4. **Slug divergences vs the mapping** (P3 — confirm intentional): `oil-gas` (mapping wanted `oil-and-gas`), `insurance` (vs `insurance-claims`), `financial-services` (vs `financial-institutions`), countit/kypit under `…/app/` (mapping wanted `/countit/` and `…/kypit/`).
5. **No cannibalization detected** between industry landings ("…software" keywords) and checklists ("…checklist" keywords).

**Note:** the walkthrough does not change the launch verdict — these are P2/P3 on-page optimizations, not blockers.

---

## 23. Correction prompts for the dev (Abe) — ready to paste

> Ready-to-paste prompts to run in the `Visualogyx/VLX-Marketing` repo (Hans-review branch). Ordered by priority. Each is self-contained. **P0 prompts are launch blockers.**

### Prompt 1 — Environment-based indexing (P0-1) 🔴
```
In the VLX-Marketing Next.js repo, the robots meta tag is currently hard-coded per page: 182 of 184 pages emit `index, follow`, while /blog/ and /contact/ emit a fixed `noindex, nofollow`. This is wrong in both directions.

Task:
1. Make the robots meta ENVIRONMENT-DRIVEN. Add a single source of truth (e.g. an env flag like SITE_ENV / NEXT_PUBLIC_SITE_ENV, or use VERCEL_ENV / the deployment host):
   - Pre-production (dev.vlx.ai, prod.vlx.ai behind Cognito, any non-public env): emit `noindex, nofollow` site-wide.
   - Production (public vlx.ai): emit `index, follow` on all public pages.
2. Remove the hard-coded `noindex, nofollow` from /blog/ and /contact/ so that in production /blog/ is `index, follow` (the blog index MUST be indexable). Decide explicitly whether /contact/ stays noindex in prod (acceptable) — but it must NOT be driven by a per-page constant that ignores the environment.
3. Implement this in the root metadata (src/app/layout.tsx `metadata.robots` / generateMetadata) so it cascades, with per-route overrides only where genuinely needed.

Verify: curl the homepage, /blog/, /contact/ and one industry page in both a pre-prod build and a production-like build; confirm the robots meta flips correctly by environment.
```

### Prompt 2 — Fix paid landing pages returning 404 (P0-2) 🔴
```
In the VLX-Marketing repo, these paid campaign landing pages currently return 404 on the live build:
/landing-inspection-software-capterra-a/, /landing-inspections-software-linkedin/, /landing-inspection-software-video/, /mold-inspection-remediation-software-landing/, /isn-vlx/, and all /lp/* routes (/lp/capterra/, /lp/g2/, /lp/linkedin/, /lp/).
Only /landing-inspection-software-g2/ and /landing-inspection-software-ads/ resolve (200).

Task (confirm the active-campaign list with marketing first):
1. For each landing page tied to an ACTIVE paid campaign, either (a) create the dedicated landing page, or (b) add a 301 in next.config.ts to the correct destination (industry/hub) if that campaign no longer needs a dedicated lander.
2. Ensure no active ad destination resolves to a 404.
Deliver the redirect/route changes in next.config.ts and/or the app router, and list which URLs now 200 vs 301 and to where.
```

### Prompt 3 — Reduce JavaScript / improve load speed (P1) 🟠
```
The new site's hub (/digital-inspections-software/) ships ~274–445 KB of gzipped JavaScript across up to 69 files, and the homepage has a large SSR DOM (~352 KB decoded, ~1,776 nodes). Historical mobile LCP on the hub was ~7.6s, limited by FCP/render path (not images — the hero is ~24 KB and already optimized).

Task:
1. Code-split and lazy-load below-the-fold sections (next/dynamic with ssr:false only where safe) on the homepage and hub.
2. Reduce client components — prefer React Server Components; move interactivity to small client islands.
3. Purge unused JS and CSS (audit the bundle; remove dead dependencies).
4. Trim the homepage DOM node count.
Goal: mobile LCP < 2.5s. Re-measure with Lighthouse (mobile, throttled) on the hub and home after the changes and report before/after LCP, FCP, TBT.
```

### Prompt 4 — Add OG images to industry pages (P1) 🟠
```
46 of 184 pages have no og:image, including the 12 industry pages (quality, virtual, field-operations, asset-verification, transportation-logistics, oil-gas, manufacturing, real-estate, home-inspectors, service-companies, vehicle, inspection-companies).

Task:
1. Add a page-specific openGraph.images (>=1200x630) via the Next.js Metadata API for each industry page (and then the remaining pages).
2. Ensure metadataBase is set in the root layout so OG image URLs are absolute.
3. Keep twitter.card = summary_large_image where an image exists.
Prioritize the 12 industry pages (highest commercial intent).
```

### Prompt 5 — Clean up the sitemap (P2) 🟡
```
In src/app/sitemap.ts, 8 URLs currently listed in the sitemap redirect instead of returning 200:
/case-studies/empowers-qa-team-with-streamlined-inspection-processes/ (-> /blog/...), and /blog/product-updates-2026/, /blog/product-updates-2025/, /blog/2020-2/, /blog/2021-2/, /blog/2022-2/, /blog/2023-2/, /blog/2024-2/ (all -> /whats-new/).

Task: exclude any URL that redirects from the sitemap so it only lists final, 200-status canonical URLs. Verify the generated sitemap.xml no longer contains those 8.
```

### Prompt 6 — Trim titles & meta descriptions (P2) 🟡
```
31 pages have titles that are empty or >60 chars and 32 pages have meta descriptions >160 chars. Worst offenders:
- Meta description 1,177 chars: /use-case/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/
- Meta descriptions 272–352 chars: the /press/* releases
- Titles 78–103 chars: /press/* releases; hub title is 71
- 10 industry pages: meta descriptions 167–210 chars

Task: rewrite titles to <=60 chars and meta descriptions to <=155 chars, keeping the target keyword near the front. Prioritize press releases and industry pages.
```

### Prompt 7 — On-page keyword alignment (P2) 🟡
```
From the keyword walkthrough, align on-page targeting to the mapped primary keywords:
1. Make the H1 keyword-led (the title already contains the keyword):
   - /digital-inspections-software/home-inspectors/ : H1 currently "Home Inspections, Completed Before You Leave the Property" -> lead with "Home Inspection Software".
   - /digital-inspections-software/app/countit/ : H1 currently "Snap, Count & Share" -> lead with the keyword, e.g. "AI Object Counting App" (this page had 3,002 GSC impressions at pos 12.6).
2. Align the TITLE to the H1 keyword:
   - /digital-inspections-software/real-estate/ : title -> "Property Inspection Software for Real Estate | VLX".
   - /digital-inspections-software/quality/ : title -> lead with "Quality Inspection Software".
3. /digital-inspections-software/hospitality/ targets "hospitality" but not "hotel", where the search volume is — work "hotel inspection / hotel maintenance" into the title, H1 and body.
4. Fix /blog/ having 2 H1 tags (should be exactly one).
```

---

---

## 24. Action plan — additional improvements (non-blocking)

> These are **improvements / migration-hygiene items**, not launch blockers and not errors that would stop the site going live. Same ready-to-paste format as §23, for the dev to run in `Visualogyx/VLX-Marketing`.

### Prompt 8 — Replicate the production redirects in the new site (improvement) 🟡
```
Replicate the following 301 redirects (exported from the current WordPress / Rank Math setup on vlx.ai) into next.config.ts of the VLX-Marketing Next.js site, so no legacy URL 404s after the migration.

Format below: `source  ->  destination  (301)`.
- Entries tagged [regex] use regular-expression matching on the source (use a regex/has-based redirect in Next.js and preserve capture groups like $1 in the destination).
- Entries tagged [start] match by prefix (source is the start of the path).
- All are permanent (301).

Rules:
- Do NOT duplicate: if a redirect for the same source is already present in next.config.ts, leave it as-is and only add the missing ones.
- Keep sources as paths relative to the site root; destinations as shown.
- After adding, build and spot-check ~10 sources (a mix of exact and regex) to confirm they 301 to the right destination in one hop.

Redirects to replicate:
privacyPolicy.html  ->  https://vlx.ai/privacy-policy/  (301)
real-estate-3/  ->  https://vlx.ai/real-estate/  (301)
real-estate-new/  ->  https://vlx.ai/real-estate/  (301)
product-feature-list/  ->  https://vlx.ai/features-list/  (301)
checklist-for-annual-apartment-inspection-in-2024/  ->  https://vlx.ai/checklist-for-annual-apartment-inspection/  (301)
financial-services-2/  ->  https://vlx.ai/financial-services/  (301)
privacy-policy-2/  ->  https://vlx.ai/privacy-policy/  (301)
tutorials/  ->  https://help.vlx.ai/  (301)
supply-chain-old/  ->  https://vlx.ai/supply-chain/  (301)
transportation-old/  ->  https://vlx.ai/transportation-logistics/  (301)
contact-old/  ->  https://vlx.ai/contact/  (301)
countit-old/  ->  https://vlx.ai/countit/  (301)
kypit-old/  ->  https://vlx.ai/kypit/  (301)
security-old/  ->  https://vlx.ai/security/  (301)
digital-inspection-platform-new/  ->  https://vlx.ai/  (301)
assets/Skip the errors/stock-photo-m  ->  https://vlx.ai/  (301)
4O7Z0HMG0ENX.js.gz  ->  https://vlx.ai/  (301)
hospitality-old/  ->  https://vlx.ai/hospitality-food-service/  (301)
construction-old/  ->  https://vlx.ai/construction/  (301)
landing-inspection-app-2/  ->  https://vlx.ai/  (301)
hr-old2/  ->  https://vlx.ai/hr/  (301)
hr-old/  ->  https://vlx.ai/hr/  (301)
home/  ->  https://vlx.ai/  (301)
product-updates-2025/  ->  https://vlx.ai/whats-new/  (301)
industries/quality-management/  ->  https://vlx.ai/quality/  (301)
category/(.*) [regex]  ->  https://vlx.ai/blog/$1  (301)
industry-trends/  ->  https://vlx.ai/blog/industry-trends/  (301)
apartment-inspection-checklist/  ->  https://vlx.ai/blog/checklist/apartment-inspection/  (301)
inspection-checklist/  ->  https://vlx.ai/blog/checklists/inspection/  (301)
safety-checklist/  ->  https://vlx.ai/blog/checklists/safety/  (301)
purchase-inspection-checklist/  ->  https://vlx.ai/blog/checklists/purchase-inspection/  (301)
write-an-incident-report/  ->  https://vlx.ai/blog/write-an-incident-report/  (301)
^precision-and-productivity-itis-inspection-transformation-with-visualogyx.* [regex]  ->  https://vlx.ai/case-studies/precision-and-productivity-itis-inspection-transformation-with-vlx/$1  (301)
^data-collection-through-digital-inspection-tool.* [regex]  ->  https://vlx.ai/blog/data-collection-through-digital-inspection-tool/$1  (301)
^quality-assurance-with-advanced-digital-inspection-software.* [regex]  ->  https://vlx.ai/blog/quality-assurance-with-advanced-digital-inspection-software/$1  (301)
^efficiency-in-construction-with-cloud-bases-audit-tools.* [regex]  ->  https://vlx.ai/blog/efficiency-in-construction-with-cloud-bases-audit-tools/$1  (301)
^digital-dispatches-in-the-transportation-business.* [regex]  ->  https://vlx.ai/blog/digital-dispatches-in-the-transportation-business/$1  (301)
^streamlining-safety-inspections-with-visualogyx.* [regex]  ->  https://vlx.ai/blog/streamlining-safety-inspections-with-vlx/$1  (301)
^from-paper-to-pixels-transforming-inspections-with-digital-tools-in-the-hospitality-sector.* [regex]  ->  https://vlx.ai/blog/from-paper-to-pixels-transforming-inspections-with-digital-tools-in-the-hospitality-sector/$1  (301)
^on-leveraging-technology-introducing-visualogyx-for-enhanced-collateral-verification.* [regex]  ->  https://vlx.ai/blog/on-leveraging-technology-introducing-vlx-for-enhanced-collateral-verification/$1  (301)
^leveraging-audit-and-inspection-platforms-to-enhance-incident-management-practices.* [regex]  ->  https://vlx.ai/blog/leveraging-audit-and-inspection-platforms-to-enhance-incident-management-practices/$1  (301)
^simplifying-object-counting-with-instacount-by-visualogyx.* [regex]  ->  https://vlx.ai/blog/simplifying-object-counting-with-instacount-by-vlx/$1  (301)
^the-evolution-of-asset-management-traditional-methods-vs-asset-inspection-software.* [regex]  ->  https://vlx.ai/blog/the-evolution-of-asset-management-traditional-methods-vs-asset-inspection-software/$1  (301)
^how-to-ensure-effective-collateral-asset-inspections.* [regex]  ->  https://vlx.ai/blog/how-to-ensure-effective-collateral-asset-inspections/$1  (301)
^enhancing-industrial-equipment-longevity-with-asset-inspection-software.* [regex]  ->  https://vlx.ai/blog/enhancing-industrial-equipment-longevity-with-asset-inspection-software/$1  (301)
^strengthening-trust-in-financial-lending-the-role-of-digital-inspections.* [regex]  ->  https://vlx.ai/blog/strengthening-trust-in-financial-lending-the-role-of-digital-inspections/$1  (301)
^strengthening-bnpl-risk-management-with-visualogyx.* [regex]  ->  https://vlx.ai/blog/strengthening-bnpl-risk-management-with-vlx/$1  (301)
blog/industry-trends/the-evolution-of-audits-balancing-traditional-and-virtual-inspections/  ->  https://vlx.ai/blog/the-evolution-of-audits-balancing-traditional-and-virtual-inspections/  (301)
the-evolution-of-audits-balancing-traditional-and-virtual-inspections/  ->  https://vlx.ai/blog/the-evolution-of-audits-balancing-traditional-and-virtual-inspections/  (301)
^a-guide-to-prevent-insurance-fraud.* [regex]  ->  https://vlx.ai/blog/a-guide-to-prevent-insurance-fraud/$1  (301)
^visualogyx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations.* [regex]  ->  https://vlx.ai/blog/vlx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/$1  (301)
^construction-industry-trends-in-the-next-10-years.* [regex]  ->  https://vlx.ai/blog/construction-industry-trends-in-the-next-10-years/$1  (301)
^visualogyx-achieves-soc-2-type-2-compliance.* [regex]  ->  https://vlx.ai/blog/vlx-achieves-soc-2-type-2-compliance/$1  (301)
^7-benefits-of-digital-hr-transformation.* [regex]  ->  https://vlx.ai/blog/7-benefits-of-digital-hr-transformation/$1  (301)
^9-key-benefits-of-supply-chain-management.* [regex]  ->  https://vlx.ai/blog/9-key-benefits-of-supply-chain-management/$1  (301)
^a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces.* [regex]  ->  https://vlx.ai/blog/a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces/$1  (301)
^the-strategic-advantage-of-integrated-management-system-ims-audits.* [regex]  ->  https://vlx.ai/blog/the-strategic-advantage-of-integrated-management-system-ims-audits/$1  (301)
^why-should-you-care-about-the-origin-and-substrates-of-imported-products.* [regex]  ->  https://vlx.ai/blog/why-should-you-care-about-the-origin-and-substrates-of-imported-products/$1  (301)
^what-you-need-to-know-about-mystery-audits-in-hotels.* [regex]  ->  https://vlx.ai/blog/what-you-need-to-know-about-mystery-audits-in-hotels/$1  (301)
^fcra-compliance-with-visualogyx-streamlined-virtual-inspections-and-authenticity-verification.* [regex]  ->  https://vlx.ai/blog/fcra-compliance-with-vlx-streamlined-virtual-inspections-and-authenticity-verification/$1  (301)
^virtual-inspections-shaping-the-future-of-industry-standards.* [regex]  ->  https://vlx.ai/blog/virtual-inspections-shaping-the-future-of-industry-standards/$1  (301)
^detecting-fraud-and-validating-authenticity-of-photos-with-visualogyxs-kypit.* [regex]  ->  https://vlx.ai/blog/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/$1  (301)
^how-often-should-fire-extinguishers-be-checked.* [regex]  ->  https://vlx.ai/blog/how-often-should-fire-extinguishers-be-checked/$1  (301)
^best-commercial-property-inspection-of-2024-a-complete-guide.* [regex]  ->  https://vlx.ai/blog/best-commercial-property-inspection-of-2024-a-complete-guide/$1  (301)
^enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-visualogyx.* [regex]  ->  https://vlx.ai/blog/enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-vlx/$1  (301)
^difference-between-sqf-audit-and-gap-audit.* [regex]  ->  https://vlx.ai/blog/difference-between-sqf-audit-and-gap-audit/$1  (301)
^kicking-off-2025-visualogyx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024.* [regex]  ->  https://vlx.ai/blog/kicking-off-2025-vlx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/$1  (301)
^is-termite-inspection-required-in-california-for-mobile-homes-a-comprehensive-guide.* [regex]  ->  https://vlx.ai/blog/is-termite-inspection-required-in-california-for-mobile-homes-a-comprehensive-guide/$1  (301)
^understanding-qapi-a-comprehensive-guide-to-quality-improvement-in-healthcare.* [regex]  ->  https://vlx.ai/blog/understanding-qapi-a-comprehensive-guide-to-quality-improvement-in-healthcare/$1  (301)
^prerensics-moving-from-containment-to-prevention.* [regex]  ->  https://vlx.ai/blog/prerensics-moving-from-containment-to-prevention/$1  (301)
^photo-authentication-software-a-shield-against-ai-generated-image-fraud.* [regex]  ->  https://vlx.ai/blog/photo-authentication-software-a-shield-against-ai-generated-image-fraud/$1  (301)
^inspection-and-quality-assurance-for-steel-pipes-in-the-oil-gas-industry.* [regex]  ->  https://vlx.ai/blog/inspection-and-quality-assurance-for-steel-pipes-in-the-oil-gas-industry/$1  (301)
^understanding-californias-sb-326-and-sb-721-balcony-inspection-laws-what-you-need-to-know.* [regex]  ->  https://vlx.ai/blog/understanding-californias-sb-326-and-sb-721-balcony-inspection-laws-what-you-need-to-know/$1  (301)
^why-container-inspections-are-crucial-for-freight-forwarders-and-iso-compliance.* [regex]  ->  https://vlx.ai/blog/why-container-inspections-are-crucial-for-freight-forwarders-and-iso-compliance/$1  (301)
^what-ispredictive-maintenance.* [regex]  ->  https://vlx.ai/blog/what-ispredictive-maintenance/$1  (301)
^fraud-proof-your-inspections-the-power-of-digital-audit-software.* [regex]  ->  https://vlx.ai/blog/fraud-proof-your-inspections-the-power-of-digital-audit-software/$1  (301)
^revolutionizing-financial-services-with-digital-audit-software.* [regex]  ->  https://vlx.ai/blog/revolutionizing-financial-services-with-digital-audit-software/$1  (301)
^digital-inspection-software-5-ways-it-transforms-audit-management.* [regex]  ->  https://vlx.ai/blog/digital-inspection-software-5-ways-it-transforms-audit-management/$1  (301)
^visualogyx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations.* [regex]  ->  https://vlx.ai/blog/vlx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations/$1  (301)
^trade-transparency-national-security-and-the-rise-of-kyp-a-perspective-by-duncan-wood.* [regex]  ->  https://vlx.ai/blog/trade-transparency-national-security-and-the-rise-of-kyp-a-perspective-by-duncan-wood/$1  (301)
^digital-inspection-vs-physical-inspection.* [regex]  ->  https://vlx.ai/blog/digital-inspection-vs-physical-inspection/$1  (301)
^on-site-inspection-methods.* [regex]  ->  https://vlx.ai/blog/on-site-inspection-methods/$1  (301)
^pushing-fraudsters-out-of-the-loop-how-visualogyx-changes-the-game-for-good.* [regex]  ->  https://vlx.ai/blog/pushing-fraudsters-out-of-the-loop-how-vlx-changes-the-game-for-good/$1  (301)
^inspection-software-that-prevents-fraud-saves-time.* [regex]  ->  https://vlx.ai/blog/inspection-software-that-prevents-fraud-saves-time/$1  (301)
^financial-software-that-cuts-risk.* [regex]  ->  https://vlx.ai/blog/financial-software-that-cuts-risk/$1  (301)
^7-modern-inspection-methods.* [regex]  ->  https://vlx.ai/blog/7-modern-inspection-methods/$1  (301)
^why-smart-companies-switch-to-digital-inspection-platforms.* [regex]  ->  https://vlx.ai/blog/why-smart-companies-switch-to-digital-inspection-platforms/$1  (301)
^duncan-wood-advocates-for-enhanced-supply-chain-transparency.* [regex]  ->  https://vlx.ai/blog/duncan-wood-advocates-for-enhanced-supply-chain-transparency/$1  (301)
^quality-assurance-process-with-advanced-digital-inspection.* [regex]  ->  https://vlx.ai/blog/quality-assurance-process-with-advanced-digital-inspection/$1  (301)
^7-modern-inspection-methods-that-boost-efficiency-save-costs.* [regex]  ->  https://vlx.ai/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/$1  (301)
^regular-packaging-inspection-for-quality-control-and-compliance.* [regex]  ->  https://vlx.ai/blog/regular-packaging-inspection-for-quality-control-and-compliance/$1  (301)
^why-supply-chain-management-software-is-essential-for-modern-logistics.* [regex]  ->  https://vlx.ai/blog/why-supply-chain-management-software-is-essential-for-modern-logistics/$1  (301)
solutions/kpirt-inspection-software/  ->  https://vlx.ai/solutions/  (301)
transportation/  ->  https://vlx.ai/transportation-logistics/  (301)
^blog/blog.* [regex]  ->  https://vlx.ai/blog/$1  (301)
safety-old/  ->  https://vlx.ai/safety/  (301)
real-estate-old/  ->  https://vlx.ai/real-estate/  (301)
quality-old/  ->  https://vlx.ai/quality/  (301)
manufacturing-old/  ->  https://vlx.ai/manufacturing/  (301)
home-new/  ->  https://vlx.ai/  (301)
features-list-old/  ->  https://vlx.ai/features-list/  (301)
product-old/  ->  https://vlx.ai/product/  (301)
vehicle-old/  ->  https://vlx.ai/vehicle/  (301)
0-theme/  ->  https://vlx.ai/  (301)
ftux-duplicate/  ->  https://vlx.ai/ftux/  (301)
whats-new-2020/  ->  https://vlx.ai/whats-new/  (301)
whats-new-2021/  ->  https://vlx.ai/whats-new/  (301)
whats-new-2022/  ->  https://vlx.ai/whats-new/  (301)
whats-new-2023/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2020-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2021-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2022-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2023-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2024-2/  ->  https://vlx.ai/whats-new/  (301)
2020-2/  ->  https://vlx.ai/whats-new/  (301)
2021-2/  ->  https://vlx.ai/whats-new/  (301)
2022-2/  ->  https://vlx.ai/whats-new/  (301)
2023-2/  ->  https://vlx.ai/whats-new/  (301)
2024-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/product-updates-2025/  ->  https://vlx.ai/whats-new/  (301)
contact-us/  ->  https://vlx.ai/contact/  (301)
blog/industry-trends/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/  ->  https://vlx.ai/blog/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/  (301)
blog/detecting-fraud-and-validating-authenticity-of-photos-with-visualogyxs-kypit/  ->  https://vlx.ai/blog/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/  (301)
detecting-fraud-and-validating-authenticity-of-photos-with-visualogyxs-kypit/  ->  https://vlx.ai/blog/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/  (301)
blog/media-coverage/vlx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/  ->  https://vlx.ai/blog/vlx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/  (301)
blog/visualogyx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/  ->  https://vlx.ai/blog/vlx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/  (301)
blog/media-coverage/kicking-off-2025-vlx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/  ->  https://vlx.ai/blog/kicking-off-2025-vlx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/  (301)
blog/kicking-off-2025-visualogyx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/  ->  https://vlx.ai/blog/kicking-off-2025-vlx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/  (301)
blog/industry-trends/simplifying-object-counting-with-instacount-by-vlx/  ->  https://vlx.ai/blog/simplifying-object-counting-with-instacount-by-vlx/  (301)
blog/simplifying-object-counting-with-instacount-by-visualogyx/  ->  https://vlx.ai/blog/simplifying-object-counting-with-instacount-by-vlx/  (301)
blog/industry-trends/fcra-compliance-with-vlx-streamlined-virtual-inspections-and-authenticity-verification/  ->  https://vlx.ai/blog/fcra-compliance-with-vlx-streamlined-virtual-inspections-and-authenticity-verification/  (301)
blog/fcra-compliance-with-visualogyx-streamlined-virtual-inspections-and-authenticity-verification/  ->  https://vlx.ai/blog/fcra-compliance-with-vlx-streamlined-virtual-inspections-and-authenticity-verification/  (301)
blog/streamlining-safety-inspections-with-visualogyx/  ->  https://vlx.ai/blog/streamlining-safety-inspections-with-vlx/  (301)
blog/media-coverage/vlx-achieves-soc-2-type-2-compliance/  ->  https://vlx.ai/blog/vlx-achieves-soc-2-type-2-compliance/  (301)
blog/visualogyx-achieves-soc-2-type-2-compliance/  ->  https://vlx.ai/blog/vlx-achieves-soc-2-type-2-compliance/  (301)
blog/industry-trends/strengthening-bnpl-risk-management-with-vlx/  ->  https://vlx.ai/blog/strengthening-bnpl-risk-management-with-vlx/  (301)
blog/strengthening-bnpl-risk-management-with-visualogyx/  ->  https://vlx.ai/blog/strengthening-bnpl-risk-management-with-vlx/  (301)
blog/media-coverage/vlx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations/  ->  https://vlx.ai/blog/vlx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations/  (301)
blog/visualogyx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations/  ->  https://vlx.ai/blog/vlx-appoints-duncan-wood-as-vp-of-strategic-accounts-and-government-relations/  (301)
case-studies/precision-and-productivity-itis-inspection-transformation-with-visualogyx/  ->  https://vlx.ai/case-studies/precision-and-productivity-itis-inspection-transformation-with-vlx/  (301)
blog/industry-trends/on-leveraging-technology-introducing-vlx-for-enhanced-collateral-verification/  ->  https://vlx.ai/blog/on-leveraging-technology-introducing-vlx-for-enhanced-collateral-verification/  (301)
blog/on-leveraging-technology-introducing-visualogyx-for-enhanced-collateral-verification/  ->  https://vlx.ai/blog/on-leveraging-technology-introducing-vlx-for-enhanced-collateral-verification/  (301)
blog/industry-trends/enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-vlx/  ->  https://vlx.ai/blog/enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-vlx/  (301)
blog/enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-visualogyx/  ->  https://vlx.ai/blog/enhancing-dealer-financial-services-dfs-with-real-time-inventory-verification-and-risk-mitigation-through-vlx/  (301)
blog/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc-2/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
blog/visualogyx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc-2/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
blog/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc-2/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
visualogyx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc-2/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
visualogyx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc-2/  ->  https://vlx.ai/media-coverage/vlx-achieves-minority-business-enterprise-mbe-certification-from-nmsdc/  (301)
blog/industry-trends/pushing-fraudsters-out-of-the-loop-how-vlx-changes-the-game-for-good/  ->  https://vlx.ai/blog/pushing-fraudsters-out-of-the-loop-how-vlx-changes-the-game-for-good/  (301)
blog/pushing-fraudsters-out-of-the-loop-how-visualogyx-changes-the-game-for-good/  ->  https://vlx.ai/blog/pushing-fraudsters-out-of-the-loop-how-vlx-changes-the-game-for-good/  (301)
blog/detecting-fraud-and-validating-authenticity-of-photos-with-visualogyxs-kypit/  ->  https://vlx.ai/blog/detecting-fraud-and-validating-authenticity-of-photos-with-vlxs-kypit/  (301)
blog/4/ [start]  ->  https://vlx.ai/blog/page/4/  (301)
^instacount-counting-objects-in-visualogyx-inspections.* [regex]  ->  https://vlx.ai/instacount-counting-objects-in-vlx-inspections/$1  (301)
blog/industry-trends/quality-assurance-with-advanced-digital-inspection-software/  ->  https://vlx.ai/blog/quality-assurance-with-advanced-digital-inspection-software/  (301)
blog/industry-trends/data-collection-through-digital-inspection-tool/  ->  https://vlx.ai/blog/data-collection-through-digital-inspection-tool/  (301)
blog/industry-trends/efficiency-in-construction-with-cloud-bases-audit-tools/  ->  https://vlx.ai/blog/efficiency-in-construction-with-cloud-bases-audit-tools/  (301)
blog/industry-trends/digital-dispatches-in-the-transportation-business/  ->  https://vlx.ai/blog/digital-dispatches-in-the-transportation-business/  (301)
blog/checklists/best-commercial-property-inspection-of-2024-a-complete-guide/  ->  https://vlx.ai/blog/best-commercial-property-inspection-of-2024-a-complete-guide/  (301)
blog/media-coverage/trade-transparency-national-security-and-the-rise-of-kyp-a-perspective-by-duncan-wood/  ->  https://vlx.ai/blog/trade-transparency-national-security-and-the-rise-of-kyp-a-perspective-by-duncan-wood/  (301)
blog/industry-trends/is-termite-inspection-required-in-california-for-mobile-homes-a-comprehensive-guide/  ->  https://vlx.ai/blog/is-termite-inspection-required-in-california-for-mobile-homes-a-comprehensive-guide/  (301)
blog/media-coverage/visualogyx-rebrands-as-vlx-establishing-a-global-standard-for-digital-inspections-and-compliance/  ->  https://vlx.ai/blog/visualogyx-rebrands-as-vlx-establishing-a-global-standard-for-digital-inspections-and-compliance/  (301)
blog/digital-inspection/why-supply-chain-management-software-is-essential-for-modern-logistics/  ->  https://vlx.ai/blog/why-supply-chain-management-software-is-essential-for-modern-logistics/  (301)
blog/industry-trends/virtual-inspections-shaping-the-future-of-industry-standards/  ->  https://vlx.ai/blog/virtual-inspections-shaping-the-future-of-industry-standards/  (301)
blog/modern-inspection-methods/7-modern-inspection-methods/  ->  https://vlx.ai/blog/7-modern-inspection-methods/  (301)
blog/industry-trends/regular-packaging-inspection-for-quality-control-and-compliance/  ->  https://vlx.ai/blog/regular-packaging-inspection-for-quality-control-and-compliance/  (301)
blog/industry-trends/photo-authentication-software-a-shield-against-ai-generated-image-fraud/  ->  https://vlx.ai/blog/photo-authentication-software-a-shield-against-ai-generated-image-fraud/  (301)
blog/industry-trends/how-often-should-fire-extinguishers-be-checked/  ->  https://vlx.ai/blog/how-often-should-fire-extinguishers-be-checked/  (301)
blog/industry-trends/9-key-benefits-of-supply-chain-management/  ->  https://vlx.ai/blog/9-key-benefits-of-supply-chain-management/  (301)
blog/inspection-software/why-smart-companies-switch-to-digital-inspection-platforms/  ->  https://vlx.ai/blog/why-smart-companies-switch-to-digital-inspection-platforms/  (301)
blog/industry-trends/a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces/  ->  https://vlx.ai/blog/a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces/  (301)
blog/industry-trends/what-ispredictive-maintenance/  ->  https://vlx.ai/blog/what-ispredictive-maintenance/  (301)
blog/industry-trends/digital-inspection-software-5-ways-it-transforms-audit-management/  ->  https://vlx.ai/blog/digital-inspection-software-5-ways-it-transforms-audit-management/  (301)
blog/industry-trends/prerensics-moving-from-containment-to-prevention/  ->  https://vlx.ai/blog/prerensics-moving-from-containment-to-prevention/  (301)
blog/industry-trends/from-paper-to-pixels-transforming-inspections-with-digital-tools-in-the-hospitality-sector/  ->  https://vlx.ai/blog/from-paper-to-pixels-transforming-inspections-with-digital-tools-in-the-hospitality-sector/  (301)
blog/industry-trends/the-evolution-of-asset-management-traditional-methods-vs-asset-inspection-software/  ->  https://vlx.ai/blog/the-evolution-of-asset-management-traditional-methods-vs-asset-inspection-software/  (301)
blog/industry-trends/7-benefits-of-digital-hr-transformation/  ->  https://vlx.ai/blog/7-benefits-of-digital-hr-transformation/  (301)
blog/industry-trends/how-to-ensure-effective-collateral-asset-inspections/  ->  https://vlx.ai/blog/how-to-ensure-effective-collateral-asset-inspections/  (301)
blog/industry-trends/the-strategic-advantage-of-integrated-management-system-ims-audits/  ->  https://vlx.ai/blog/the-strategic-advantage-of-integrated-management-system-ims-audits/  (301)
blog/industry-trends/construction-industry-trends-in-the-next-10-years/  ->  https://vlx.ai/blog/construction-industry-trends-in-the-next-10-years/  (301)
blog/industry-trends/what-you-need-to-know-about-mystery-audits-in-hotels/  ->  https://vlx.ai/blog/what-you-need-to-know-about-mystery-audits-in-hotels/  (301)
blog/industry-trends/leveraging-audit-and-inspection-platforms-to-enhance-incident-management-practices/  ->  https://vlx.ai/blog/leveraging-audit-and-inspection-platforms-to-enhance-incident-management-practices/  (301)
blog/industry-trends/why-should-you-care-about-the-origin-and-substrates-of-imported-products/  ->  https://vlx.ai/blog/why-should-you-care-about-the-origin-and-substrates-of-imported-products/  (301)
blog/industry-trends/enhancing-industrial-equipment-longevity-with-asset-inspection-software/  ->  https://vlx.ai/blog/enhancing-industrial-equipment-longevity-with-asset-inspection-software/  (301)
blog/industry-trends/strengthening-trust-in-financial-lending-the-role-of-digital-inspections/  ->  https://vlx.ai/blog/strengthening-trust-in-financial-lending-the-role-of-digital-inspections/  (301)
blog/industry-trends/a-guide-to-prevent-insurance-fraud/  ->  https://vlx.ai/blog/a-guide-to-prevent-insurance-fraud/  (301)
blog/industry-trends/difference-between-sqf-audit-and-gap-audit/  ->  https://vlx.ai/blog/difference-between-sqf-audit-and-gap-audit/  (301)
blog/industry-trends/understanding-qapi-a-comprehensive-guide-to-quality-improvement-in-healthcare/  ->  https://vlx.ai/blog/understanding-qapi-a-comprehensive-guide-to-quality-improvement-in-healthcare/  (301)
blog/industry-trends/inspection-and-quality-assurance-for-steel-pipes-in-the-oil-gas-industry/  ->  https://vlx.ai/blog/inspection-and-quality-assurance-for-steel-pipes-in-the-oil-gas-industry/  (301)
blog/industry-trends/understanding-californias-sb-326-and-sb-721-balcony-inspection-laws-what-you-need-to-know/  ->  https://vlx.ai/blog/understanding-californias-sb-326-and-sb-721-balcony-inspection-laws-what-you-need-to-know/  (301)
blog/industry-trends/why-container-inspections-are-crucial-for-freight-forwarders-and-iso-compliance/  ->  https://vlx.ai/blog/why-container-inspections-are-crucial-for-freight-forwarders-and-iso-compliance/  (301)
blog/product/fraud-proof-your-inspections-the-power-of-digital-audit-software/  ->  https://vlx.ai/blog/fraud-proof-your-inspections-the-power-of-digital-audit-software/  (301)
blog/industry-trends/revolutionizing-financial-services-with-digital-audit-software/  ->  https://vlx.ai/blog/revolutionizing-financial-services-with-digital-audit-software/  (301)
blog/inspection-tools/digital-inspection-vs-physical-inspection/  ->  https://vlx.ai/blog/digital-inspection-vs-physical-inspection/  (301)
blog/industry-trends/on-site-inspection-methods/  ->  https://vlx.ai/blog/on-site-inspection-methods/  (301)
blog/inspection-software/inspection-software-that-prevents-fraud-saves-time/  ->  https://vlx.ai/blog/inspection-software-that-prevents-fraud-saves-time/  (301)
blog/inspection-software/financial-software-that-cuts-risk/  ->  https://vlx.ai/blog/financial-software-that-cuts-risk/  (301)
blog/media-coverage/duncan-wood-advocates-for-enhanced-supply-chain-transparency/  ->  https://vlx.ai/blog/duncan-wood-advocates-for-enhanced-supply-chain-transparency/  (301)
blog/digital-inspection/7-modern-inspection-methods-that-boost-efficiency-save-costs/  ->  https://vlx.ai/blog/7-modern-inspection-methods-that-boost-efficiency-save-costs/  (301)
blog/case-studies/  ->  https://vlx.ai/case-studies/  (301)
blog/uncategorized/  ->  https://vlx.ai/blog/  (301)
blog/inspection-checklist/  ->  https://vlx.ai/blog/checklists/inspection/  (301)
blog/use-case/  ->  https://vlx.ai/use-case/  (301)
blog/use-case/page/2/  ->  https://vlx.ai/use-case/page/2/  (301)
blog/safety-checklist/  ->  https://vlx.ai/blog/checklists/safety/  (301)
blog/purchase-inspection-checklist/  ->  https://vlx.ai/blog/checklists/purchase-inspection/  (301)
prerensics-moving-from-containment-to-prevention/  ->  https://vlx.ai/blog/prerensics-moving-from-containment-to-prevention/  (301)
^product.* [regex]  ->  https://vlx.ai/digital-inspections-software/$1  (301)
^brand.* [regex]  ->  https://vlx.ai/about-us/brand/$1  (301)
^brochure.* [regex]  ->  https://vlx.ai/about-us/brochure/$1  (301)
^contact.* [regex]  ->  https://vlx.ai/about-us/contact/$1  (301)
^email-signature.* [regex]  ->  https://vlx.ai/about-us/email-signature/$1  (301)
^privacy-policy.* [regex]  ->  https://vlx.ai/about-us/privacy-policy/$1  (301)
^cookie-policy.* [regex]  ->  https://vlx.ai/about-us/cookie-policy/$1  (301)
^terms-of-service.* [regex]  ->  https://vlx.ai/about-us/terms-of-service/$1  (301)
^accessibility-statement.* [regex]  ->  https://vlx.ai/about-us/accessibility-statement/$1  (301)
^disclaimer.* [regex]  ->  https://vlx.ai/about-us/disclaimer/$1  (301)
^eula.* [regex]  ->  https://vlx.ai/about-us/eula/$1  (301)
blog/leading-fintech-uses-virtual-inspections-to-eliminate-vendor-fraud/  ->  https://vlx.ai/blog/how-one-fintech-uses-virtual-inspections-to-stop-fraud/  (301)
blog/the-ghost-center-scandal-why-traditional-audits-failed-and-how-live-verification-would-have-stopped-it/  ->  https://vlx.ai/blog/why-audits-failed-without-real-time-inspections/  (301)
^blog/([^/]*checklist[^/]*)/?$ [regex]  ->  https://vlx.ai/checklists/$1/  (301)
^digital-inspection-software/(.*)$ [regex]  ->  https://vlx.ai/digital-inspections-software/$1  (301)
enterprise/  ->  https://vlx.ai/digital-inspections-software/$1  (301)
business/  ->  https://vlx.ai/digital-inspections-software/$1  (301)
^product/(.*)$ [regex]  ->  https://vlx.ai/digital-inspections-software/$1  (301)
^the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction.* [regex]  ->  https://vlx.ai/use-case/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/  (301)
blog/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/  ->  https://vlx.ai/use-case/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/  (301)
product/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/  ->  https://vlx.ai/use-case/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/  (301)
the-ultimate-crane-inspection-checklist-ensuring-safety-and-efficiency/  ->  https://vlx.ai/checklists/crane-inspection-checklist/  (301)
crane-inspection-checklist/  ->  https://vlx.ai/checklists/crane-inspection-checklist/  (301)
blog/crane-inspection-checklist/  ->  https://vlx.ai/checklists/crane-inspection-checklist/  (301)
blog/checklists/crane-inspection-checklist/  ->  https://vlx.ai/checklists/crane-inspection-checklist/  (301)
iso-9001-audit-checklist/  ->  https://vlx.ai/checklists/iso-9001-audit-checklist/  (301)
blog/iso-9001-audit-checklist/  ->  https://vlx.ai/checklists/iso-9001-audit-checklist/  (301)
blog/checklists/iso-9001-audit-checklist/  ->  https://vlx.ai/checklists/iso-9001-audit-checklist/  (301)
blog/checklists/inspection/health-inspection-checklist-for-restaurants/  ->  https://vlx.ai/checklists/health-inspection-checklist-for-restaurants/  (301)
blog/health-inspection-checklist-for-restaurants/  ->  https://vlx.ai/checklists/health-inspection-checklist-for-restaurants/  (301)
checklists/health-inspection-checklist/  ->  https://vlx.ai/checklists/health-inspection-checklist-for-restaurants/  (301)
health-inspection-checklist-for-restaurants/  ->  https://vlx.ai/checklists/health-inspection-checklist-for-restaurants/  (301)
blog/health-inspection-checklist/  ->  https://vlx.ai/checklists/health-inspection-checklist-for-restaurants/  (301)
visualogyx-form-data-capture-capabilities/  ->  https://vlx.ai/forms/vlx-form-data-capture-capabilities/  (301)
blog/vlx-form-data-capture-capabilities/  ->  https://vlx.ai/forms/vlx-form-data-capture-capabilities/  (301)
blog/industry-trends/the-power-to-know-transforming-lending-decisions-through-technology/  ->  https://vlx.ai/case-studies/the-power-to-know-transforming-lending-decisions-through-technology/  (301)
blog/the-power-to-know-transforming-lending-decisions-through-technology/  ->  https://vlx.ai/case-studies/the-power-to-know-transforming-lending-decisions-through-technology/  (301)
blog/industry-trends/checklist-for-annual-apartment-inspection/  ->  https://vlx.ai/checklists/checklist-for-annual-apartment-inspection/  (301)
blog/checklist-for-annual-apartment-inspection/  ->  https://vlx.ai/checklists/checklist-for-annual-apartment-inspection/  (301)
checklist-for-annual-apartment-inspection/  ->  https://vlx.ai/checklists/checklist-for-annual-apartment-inspection/  (301)
what-to-look-for-when-renting-a-house/  ->  https://vlx.ai/checklists/what-to-look-for-when-renting-a-houses/  (301)
blog/what-to-look-for-when-renting-a-house/  ->  https://vlx.ai/checklists/what-to-look-for-when-renting-a-houses/  (301)
a-comprehensive-guide-to-a-successful-5s-audit-with-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
blog/industry-trends/5s-audit-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
blog/5s-audit-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
5s-audit-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
blog/industry-trends/5s-audit-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
blog/5s-audit-checklist/  ->  https://vlx.ai/checklists/5s-audit-checklist/  (301)
house-insurance-inspection-checklist/  ->  https://vlx.ai/checklists/house-insurance-inspection-checklist/  (301)
blog/house-insurance-inspection-checklist/  ->  https://vlx.ai/checklists/house-insurance-inspection-checklist/  (301)
blog/checklists/house-insurance-inspection/house-insurance-inspection-checklist/  ->  https://vlx.ai/checklists/house-insurance-inspection-checklist/  (301)
pre-drywall-inspection-checklist/  ->  https://vlx.ai/checklists/pre-drywall-inspection-checklist/  (301)
blog/pre-drywall-inspection-checklist/  ->  https://vlx.ai/checklists/pre-drywall-inspection-checklist/  (301)
blog/checklists/pre-drywall-inspection-checklist/  ->  https://vlx.ai/checklists/pre-drywall-inspection-checklist/  (301)
blog/media-coverage/empowers-qa-team-with-streamlined-inspection-processes/  ->  https://vlx.ai/case-studies/empowers-qa-team-with-streamlined-inspection-processes/  (301)
blog/empowers-qa-team-with-streamlined-inspection-processes/  ->  https://vlx.ai/case-studies/empowers-qa-team-with-streamlined-inspection-processes/  (301)
pre-purchase-inspection-checklist/  ->  https://vlx.ai/checklists/pre-purchase-inspection-checklist/  (301)
blog/pre-purchase-inspection-checklist/  ->  https://vlx.ai/checklists/pre-purchase-inspection-checklist/  (301)
blog/checklists/pre-purchase-inspection-checklist/  ->  https://vlx.ai/checklists/pre-purchase-inspection-checklist/  (301)
blog/industry-trends/how-digital-inspection-technology-is-transforming-asset-based-lending-for-financial-institutions/  ->  https://vlx.ai/blog/digital-inspections-transforming-asset-based-lending/  (301)
blog/how-digital-inspection-technology-is-transforming-asset-based-lending-for-financial-institutions/  ->  https://vlx.ai/blog/digital-inspections-transforming-asset-based-lending/  (301)
how-digital-inspection-technology-is-transforming-asset-based-lending-for-financial-institutions/  ->  https://vlx.ai/blog/digital-inspections-transforming-asset-based-lending/  (301)
blog/media-coverage/empowers-qa-team-with-streamlined-inspection-processes/  ->  https://vlx.ai/blog/empowers-qa-team-with-streamlined-inspection-processes/  (301)
blog/industry-trends/supply-chain-transparency-integrating-product-genealogy-with-vlx/  ->  https://vlx.ai/case-studies/supply-chain-transparency-integrating-product-genealogy-with-vlx/  (301)
blog/supply-chain-transparency-integrating-product-genealogy-with-vlx/  ->  https://vlx.ai/case-studies/supply-chain-transparency-integrating-product-genealogy-with-vlx/  (301)
blog/industry-trends/understanding-layered-process-audits-lpas/  ->  https://vlx.ai/case-studies/understanding-layered-process-audits-lpas/  (301)
blog/understanding-layered-process-audits-lpas/  ->  https://vlx.ai/case-studies/understanding-layered-process-audits-lpas/  (301)
blog/checklist-for-real-estate-inspection/  ->  https://vlx.ai/checklists/  (301)
blog/checklist/  ->  https://vlx.ai/checklists/  (301)
blog//checklist/  ->  https://vlx.ai/checklists/  (301)
blog/checklists/real-estate-inspection/  ->  https://vlx.ai/checklists/  (301)
blog/checklists/  ->  https://vlx.ai/checklists/  (301)
blog/checklists/6s-lean-audit-checklist/  ->  https://vlx.ai/checklists/6s-lean-audit-checklist/  (301)
blog/6s-lean-audit-checklist/  ->  https://vlx.ai/checklists/6s-lean-audit-checklist/  (301)
6s-lean-audit-checklist/  ->  https://vlx.ai/checklists/6s-lean-audit-checklist/  (301)
a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/  ->  https://vlx.ai/checklists/a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/  (301)
blog/a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/  ->  https://vlx.ai/checklists/a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/  (301)
blog/checklists/best-furnace-maintenance-checklist/  ->  https://vlx.ai/checklists/best-furnace-maintenance-checklist/  (301)
blog/best-furnace-maintenance-checklist/  ->  https://vlx.ai/checklists/best-furnace-maintenance-checklist/  (301)
best-furnace-maintenance-checklist/  ->  https://vlx.ai/checklists/best-furnace-maintenance-checklist/  (301)
blog/industry-trends/best-gemba-walk-checklist-by-experts-get-the-template/  ->  https://vlx.ai/checklists/best-gemba-walk-checklist-by-experts-get-the-template/  (301)
best-gemba-walk-checklist-by-experts-get-the-template/  ->  https://vlx.ai/checklists/best-gemba-walk-checklist-by-experts-get-the-template/  (301)
blog/best-gemba-walk-checklist-by-experts-get-the-template/  ->  https://vlx.ai/checklists/best-gemba-walk-checklist-by-experts-get-the-template/  (301)
blog/checklists/boiler-maintenance-checklist/  ->  https://vlx.ai/checklists/boiler-maintenance-checklist/  (301)
boiler-maintenance-checklist/  ->  https://vlx.ai/checklists/boiler-maintenance-checklist/  (301)
blog/boiler-maintenance-checklist/  ->  https://vlx.ai/checklists/boiler-maintenance-checklist/  (301)
brc-audit-checklist-requirements-and-key-insights/  ->  https://vlx.ai/checklists/brc-audit-checklist-requirements-and-key-insights/  (301)
blog/checklists/brc-audit-checklist-requirements-and-key-insights/  ->  https://vlx.ai/checklists/brc-audit-checklist-requirements-and-key-insights/  (301)
blog/brc-audit-checklist-requirements-and-key-insights/  ->  https://vlx.ai/checklists/brc-audit-checklist-requirements-and-key-insights/  (301)
blog/checklists/cdl-pre-trip-inspection-checklist/  ->  https://vlx.ai/checklists/cdl-pre-trip-inspection-checklist/  (301)
cdl-pre-trip-inspection-checklist/  ->  https://vlx.ai/checklists/cdl-pre-trip-inspection-checklist/  (301)
blog/cdl-pre-trip-inspection-checklist/  ->  https://vlx.ai/checklists/cdl-pre-trip-inspection-checklist/  (301)
blog/industry-trends/commercial-building-inspection-checklist/  ->  https://vlx.ai/checklists/commercial-building-inspection-checklist/  (301)
commercial-building-inspection-checklist/  ->  https://vlx.ai/checklists/commercial-building-inspection-checklist/  (301)
blog/commercial-building-inspection-checklist/  ->  https://vlx.ai/checklists/commercial-building-inspection-checklist/  (301)
blog/checklists/food-safety-checklist/  ->  https://vlx.ai/checklists/food-safety-checklist/  (301)
food-safety-checklist/  ->  https://vlx.ai/checklists/food-safety-checklist/  (301)
blog/food-safety-checklist/  ->  https://vlx.ai/checklists/food-safety-checklist/  (301)
blog/checklists/inspection/home-safety/home-safety-checklist-for-seniors/  ->  https://vlx.ai/checklists/home-safety-checklist-for-seniors/  (301)
blog/home-safety-checklist/  ->  https://vlx.ai/checklists/home-safety-checklist-for-seniors/  (301)
home-safety-checklist-for-seniors/  ->  https://vlx.ai/checklists/home-safety-checklist-for-seniors/  (301)
blog/home-safety-checklist-for-seniors/  ->  https://vlx.ai/checklists/home-safety-checklist-for-seniors/  (301)
checklists/home-safety-checklist/  ->  https://vlx.ai/checklists/home-safety-checklist-for-seniors/  (301)
blog/checklists/home-safety-inspection-checklist/  ->  https://vlx.ai/checklists/home-safety-inspection-checklist/  (301)
home-safety-inspection-checklist/  ->  https://vlx.ai/checklists/home-safety-inspection-checklist/  (301)
blog/home-safety-inspection-checklist/  ->  https://vlx.ai/checklists/home-safety-inspection-checklist/  (301)
hotel-maintenance-checklist/  ->  https://vlx.ai/checklists/hotel-maintenance-checklist/  (301)
blog/checklists/hotel-maintenance-checklist/  ->  https://vlx.ai/checklists/hotel-maintenance-checklist/  (301)
blog/hotel-maintenance-checklist/  ->  https://vlx.ai/checklists/hotel-maintenance-checklist/  (301)
blog/checklists/how-to-create-the-best-sox-compliance-checklist/  ->  https://vlx.ai/checklists/how-to-create-the-best-sox-compliance-checklist/  (301)
blog/how-to-create-the-best-sox-compliance-checklist/  ->  https://vlx.ai/checklists/how-to-create-the-best-sox-compliance-checklist/  (301)
blog/product/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  ->  https://vlx.ai/checklists/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  (301)
how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  ->  https://vlx.ai/checklists/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  (301)
blog/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  ->  https://vlx.ai/checklists/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/  (301)
human-resource-compliance-checklist/  ->  https://vlx.ai/checklists/human-resource-compliance-checklist/  (301)
blog/checklists/human-resource-compliance-checklist/  ->  https://vlx.ai/checklists/human-resource-compliance-checklist/  (301)
blog/human-resource-compliance-checklist/  ->  https://vlx.ai/checklists/human-resource-compliance-checklist/  (301)
blog/checklists/mobile-home-inspection-checklist/  ->  https://vlx.ai/checklists/mobile-home-inspection-checklist/  (301)
mobile-home-inspection-checklist/  ->  https://vlx.ai/checklists/mobile-home-inspection-checklist/  (301)
blog/mobile-home-inspection-checklist/  ->  https://vlx.ai/checklists/mobile-home-inspection-checklist/  (301)
mold-inspection-checklist/  ->  https://vlx.ai/checklists/mold-inspection-checklist/  (301)
blog/checklists/mold-inspection-checklist/  ->  https://vlx.ai/checklists/mold-inspection-checklist/  (301)
blog/mold-inspection-checklist/  ->  https://vlx.ai/checklists/mold-inspection-checklist/  (301)
osha-compliance-checklist/  ->  https://vlx.ai/checklists/osha-compliance-checklist/  (301)
blog/checklists/osha-compliance-checklist/  ->  https://vlx.ai/checklists/osha-compliance-checklist/  (301)
blog/osha-compliance-checklist/  ->  https://vlx.ai/checklists/osha-compliance-checklist/  (301)
personal-protective-equipment-checklist/  ->  https://vlx.ai/checklists/personal-protective-equipment-checklist/  (301)
blog/checklists/personal-protective-equipment-checklist/  ->  https://vlx.ai/checklists/personal-protective-equipment-checklist/  (301)
blog/personal-protective-equipment-checklist/  ->  https://vlx.ai/checklists/personal-protective-equipment-checklist/  (301)
blog/product/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  ->  https://vlx.ai/checklists/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  (301)
rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  ->  https://vlx.ai/checklists/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  (301)
blog/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  ->  https://vlx.ai/checklists/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/  (301)
blog/security-audit-checklist/  ->  https://vlx.ai/checklists/security-audit-checklist/  (301)
blog/checklists/security-audit-checklist/  ->  https://vlx.ai/checklists/security-audit-checklist/  (301)
security-audit-checklist/  ->  https://vlx.ai/checklists/security-audit-checklist/  (301)
blog/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  ->  https://vlx.ai/checklists/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  (301)
the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  ->  https://vlx.ai/checklists/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  (301)
blog/checklists/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  ->  https://vlx.ai/checklists/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/  (301)
blog/checklists/truck-inspection-checklist/  ->  https://vlx.ai/checklists/truck-inspection-checklist/  (301)
blog/truck-inspection-checklist/  ->  https://vlx.ai/checklists/truck-inspection-checklist/  (301)
truck-inspection-checklist-with-free-pdf/  ->  https://vlx.ai/checklists/truck-inspection-checklist/  (301)
truck-inspection-checklist/  ->  https://vlx.ai/checklists/truck-inspection-checklist/  (301)
blog/warehouse-safety-checklist/  ->  https://vlx.ai/checklists/warehouse-safety-checklist/  (301)
warehouse-safety-checklist/  ->  https://vlx.ai/checklists/warehouse-safety-checklist/  (301)
title-step-by-step-guide-to-developing-an-effective-warehouse-safety-checklist/  ->  https://vlx.ai/checklists/warehouse-safety-checklist/  (301)
blog/industry-trends/warehouse-safety-checklist/  ->  https://vlx.ai/checklists/warehouse-safety-checklist/  (301)
blog/enhance-inspection-accuracy-and-fraud-detection-with-vlxs-kypit/  ->  https://vlx.ai/kypit/enhance-inspection-accuracy-and-fraud-detection-with-vlxs-kypit/  (301)
blog/enhance-inspection-accuracy-and-fraud-detection-with-visualogyxs-kypit/  ->  https://vlx.ai/kypit/enhance-inspection-accuracy-and-fraud-detection-with-vlxs-kypit/  (301)
enhance-inspection-accuracy-and-fraud-detection-with-visualogyxs-kypit/  ->  https://vlx.ai/kypit/enhance-inspection-accuracy-and-fraud-detection-with-vlxs-kypit/  (301)
blog/how-to-write-an-incident-report/  ->  https://vlx.ai/reports/how-to-write-an-incident-report/  (301)
blog/digital-inspection/how-to-write-an-incident-report/  ->  https://vlx.ai/reports/how-to-write-an-incident-report/  (301)
how-to-write-an-incident-report/  ->  https://vlx.ai/reports/how-to-write-an-incident-report/  (301)
blog/how-to-efficiently-manage-a-cleaning-service-business/  ->  https://vlx.ai/use-case/how-to-efficiently-manage-a-cleaning-service-business/  (301)
how-to-efficiently-manage-a-cleaning-service-business/  ->  https://vlx.ai/use-case/how-to-efficiently-manage-a-cleaning-service-business/  (301)
blog/working-offline-and-syncing-later-in-digital-inspection-apps/  ->  https://vlx.ai/use-case/working-offline-and-syncing-later-in-digital-inspection-apps/  (301)
working-offline-and-syncing-later-in-digital-inspection-apps/  ->  https://vlx.ai/use-case/working-offline-and-syncing-later-in-digital-inspection-apps/  (301)
visualogyx-checklist-app/  ->  https://vlx.ai/vlx-checklist-app/  (301)
product-updates/product-updates-2025/  ->  https://vlx.ai/whats-new/  (301)
blog/product-updates/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2024-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2023-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2022-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2021-2/  ->  https://vlx.ai/whats-new/  (301)
product-updates/2020-2/  ->  https://vlx.ai/whats-new/  (301)
^(digital-inspection-software|product|enterprise|business|vehicle|quality|oil-and-gas|hospitality|supply-chain|safety|transportation-logistics|construction|manufacturing|hr|financial-institutions|insurance-claims)$ [regex]  ->  https://vlx.ai/digital-inspections-software/$1/  (301)
^(real-state|real-estate|digital-inspection-software\\/real-state|digital-inspections-software\\/real-state)\\/? [regex]  ->  https://vlx.ai/digital-inspections-software/real-estate/  (301)
^digital-inspection-software\\/? [regex]  ->  https://vlx.ai/digital-inspections-software/  (301)
^hospitality-food-service\\/? [regex]  ->  https://vlx.ai/digital-inspections-software/hospitality/  (301)
^virtual\\/?  ->  https://vlx.ai/digital-inspections-software/virtual/  (301)
oil-and-gas-inspection-software/  ->  https://vlx.ai/digital-inspections-software/oil-and-gas/  (301)
landing-inspection-software-/  ->  https://vlx.ai/digital-inspections-software/  (301)
financial-services  ->  https://vlx.ai/digital-inspections-software/financial-institutions/  (301)
^(asset-based-lending|buy-now-pay-later)$ [regex]  ->  https://vlx.ai/digital-inspections-software/financial-institutions/$1/  (301)

```

### Prompt 9 — Landing-page slug convention (improvement) 🟡
```
Convention for all campaign / marketing landing pages in the VLX-Marketing site: every landing page MUST contain the word "landing" in its URL slug (e.g. /landing-inspection-software-capterra/).

Task:
1. Review all existing landing pages / campaign routes in the repo.
2. If a landing page ALREADY contains "landing" in its slug, do NOT modify it — leave it exactly as is.
3. If a campaign landing page does NOT contain "landing" in its slug, rename it so the slug includes "landing", and add a 301 from the old slug to the new one.
4. Apply this convention to every new landing page created from now on.
This is a naming-consistency improvement, not a blocker.
```

## 📝 Progress log
- **2026-07-16:** Audit completed on `dev.vlx.ai` (build confirmed identical to `prod.vlx.ai` by the team). Authenticated `fetch()` crawler over 184 URLs + probes + lab performance. English MD published to `vlx-seo/vlx-seo-plan/reports/`, English HTML published to GitHub Pages. Correction prompts (§23) added to the MD per client request (MD only — not in the HTML). Field LCP → measure with PageSpeed post-Cognito.
