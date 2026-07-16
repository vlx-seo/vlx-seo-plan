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

## 📝 Progress log
- **2026-07-16:** Audit completed on `dev.vlx.ai` (build confirmed identical to `prod.vlx.ai` by the team). Authenticated `fetch()` crawler over 184 URLs + probes + lab performance. English MD published to `vlx-seo/vlx-seo-plan/reports/`, English HTML published to GitHub Pages. Correction prompts (§23) added to the MD per client request (MD only — not in the HTML). Field LCP → measure with PageSpeed post-Cognito.
