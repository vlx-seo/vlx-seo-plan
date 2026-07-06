# SEO Audit Re-validation — VLX (fresh Hans-review branch)

**Date:** 2026-07-04
**Auditor:** Laura / Software Craft CR
**Audited object:** branch `Hans-review` @ `fb5e9b2` (2026-06-30) of `Visualogyx/VLX-Marketing` — source code (isolated worktree)
**Baseline:** pre-migration audit 2026-06-19 (SEO Health 81/100)

---

## Why this re-validation

The **2026-07-01** review audited the `dev.vlx.ai` environment, which turned out to be running a **stale build**. That produced "new errors" that did not correspond to the actual latest code. This re-validation audits the **fresh branch code** (the latest version) in order to: (1) confirm that the June 19 audit was still valid, and (2) separate the July 1 false positives from the real open items.

**Method:** static code audit over the branch worktree (`next.config.ts`, `sitemap.ts`, schema, renderer, `*Content.tsx`, `Navbar`/`Footer`, blog routes). The post content lives in Payload/Neon (DB shared with `dev.vlx.ai`) → content issues are validated against the "Blog Posts Review" Sheet. Real performance (LCP) is not re-measurable without a deployed, browsable environment.

**Note on indexing (important):** the pre-production environment (`dev.vlx.ai` and any preview subdomain) is `noindex, nofollow` **by design** (a `VERCEL_ENV` check in the layout) until the production launch at `https://vlx.ai`. That `noindex` is **expected and is NOT counted as a finding or blocker**. The only `noindex` mentioned in this document —the compare pages— is a deliberate *soft-unpublish* (phase 1), unrelated to the environment. Launch check (non-blocking): when moving to `vlx.ai`, confirm that indexing is enabled.

---

## Verdict

**The June 19 audit was correct.** In the fresh branch, **all technical must-fixes are resolved except the known paid landing pages blocker**. Most of the "new findings" from the July 1 review were **false positives from the old build**.

---

## 1. Status of the 7 must-fixes from June 19 (in `fb5e9b2`)

| # | Must-fix | Status | Evidence |
|---|---|---|---|
| 1 | Hub LCP 7.9s | ⏳ Pending measurement on deploy | Not re-measurable in static code |
| 2 | Fabricated `aggregateRating` | ✅ RESOLVED | Removed; 0 occurrences (`src/lib/schema.ts:89-93`) |
| 3 | Sitemap with 5 categories 308 | ✅ RESOLVED | Only 4 live categories (`sitemap.ts:175-188`) |
| 4 | 2 `/use-case/` redirects | ✅ RESOLVED | `next.config.ts:405-414` |
| 5 | Paid landing pages | ❌ OPEN (blocker) | 0/13 redirect (`next.config.ts`, `campaigns.ts:11-66`) |
| 6 | Homepage H1 "VerificationInspections" | ✅ RESOLVED | `HeroTypewriter.tsx:67-81`, clean SSR |
| 7 | Blog SSR + crawlable pagination | ✅ RESOLVED | `/blog/page/N` path-based, `index:true` |

---

## 2. Re-validation of the "new findings" from the July 1 review

| July 1 finding | Verdict in fresh branch | Evidence |
|---|---|---|
| **NEW-1**: `/blog/page/N` → noindex + canonical to `/blog/` | ✅ **FALSE POSITIVE** (old build) | Path-based SSR, own canonical, `robots index:true` (`blog/page/[page]/page.tsx:79-81`) |
| **NEW-2**: old top-level slugs `/oil-and-gas/`, `/financial-institutions/` return 404 | 🟡 **REAL minor** | They redirect under the hub but not at top-level; 2 rules missing (`next.config.ts:195-198,270-283`) |
| **NEW-3**: titles/meta out of range (39/47 of 185) | 🟡 **PARTIALLY OPEN** | No length guardrail; blog takes title/desc from the CMS (`blog/[slug]/page.tsx:128-129`). Counts not verifiable in code |
| **NEW-4**: compare pages gained schema | ✅ **MISATTRIBUTED** | `Organization+WebSite` is global (`layout.tsx:67-74`); compare pages are **noindex** (unpublished) |
| **NEW-5**: 32/37 hub SVGs without `alt` | ⚪ Minor (not re-audited in depth) | — |
| **NEW-6**: sitemap 175→185 URLs | ⚪ Informational | Normal content growth |
| OG "46/185 without `og:image`" | ✅ **PARTIAL FALSE POSITIVE** (old build) | 16 routes with their own `opengraph-image.tsx` since June 22 (commit `87557e1`) — exceeds the top-10 target |

---

## 3. Blogs

### 3a. System (code) — ✅ solid
- **Renderer** (`RichTextRenderer.tsx`): standard Payload/Lexical converters + normalizes the broken links from the WordPress migration.
- **Path-based pagination** `/blog/page/N` and `/blog/category/*/page/N`, indexable, with real SSR `<a href>`.
- **Sitemap**: only 4 live categories (200); posts routed by canonical category (`/checklists/`, `/use-case/`, `/forms/`, `/reports/`, `/blog/`).
- **Schema**: `Article` + `BreadcrumbList` (author not fabricated when the CMS doesn't provide it).

### 3b. Content ("Blog Posts Review", 114 URLs) — ⚠️ CMS fixes

Distribution: 61 `/blog/` · 35 `/checklists/` · 10 `/use-case/` · 2 `/forms/` · 1 `/reports/` · 5 other.

| Issue category | Affected URLs | Priority | Who fixes it |
|---|---|---|---|
| Images that don't load | **12** | 🔴 **BLOCKER** | ✏️ Client (CMS) |
| Lost list structure | **77** | 🟠 **P1** | ⚙️ Developer |
| Unoptimized meta description | 108 | 🟡 P2 | ✏️ Client (CMS) |
| Heading hierarchy (h2/h3/h4) | 39 | 🟡 P2 | ✏️ Client (CMS) |
| Missing content | 3 | 🟡 P2 | ✏️ Client (CMS) |
| Broken CTAs/links | 2 | 🟡 P2 | ✏️ Client (CMS) |
| Incomplete tables | 1 | 🟡 P2 | ⚙️ Developer |
| Broken display/HTML | 1 | 🟡 P2 | ⚙️ Developer |

**Final classification:** the **images that don't load (12)** are a **blocker** — the site should not launch with visibly broken content. The **poorly structured lists (77)** are **P1**. The rest is **P2** (meta descriptions 108, headings 39, content 3, CTAs 2, table 1, display 1).

These are **content issues in Payload CMS** (WP→Payload migration), **not code issues** — most are fixed in the CMS, not in the branch. The three big ones (descriptions, lists, headings) are **systematic**: the rich text import lost list/heading structure and the meta descriptions were not optimized.

**Who can fix each item:**
- ✏️ **Editable by the client in the CMS (Payload), no code:** images (re-upload), meta descriptions, missing content, CTAs (re-link), and the heading **level** (h2/h3/h4).
- ⚙️ **Requires a developer (code):** poorly structured lists, tables, broken display, and headings nested inside lists — because the Payload editor cannot create that HTML.

---

## 4. REAL open items (prioritized)

1. **Paid landing pages** — P0 / blocker. 0/13 redirect; `/lp/[campaign]/` only has 3 new slugs; the old campaigns return `notFound()`. Hans must create the dedicated landers/redirects.
2. **Blog images that don't load (12)** — 🔴 blocker. Visibly broken content; don't launch like this. ✏️ The client re-uploads them in the CMS (Payload), no code.
3. **Poorly structured lists in blogs (77)** — P1. The rich text import lost the list structure. ⚙️ Requires a developer: the Payload editor cannot create that HTML.
4. **Rest of blog content (CMS)** — P2. 108 descriptions + 39 headings + 3 missing content + 2 CTAs + 1 table + 1 display. ✏️ Descriptions, headings (level), content, and CTAs are edited by the client in the CMS; ⚙️ table and display require a developer. Editorial work in the CMS (several in batch).
5. **2 top-level redirects** — P2. `/oil-and-gas/` and `/financial-institutions/` → their destinations under the hub.
6. **Title/description length guardrail** — P2 / CMS discipline.
7. **Hub LCP** — P1 (inherited from June 19). Measure on a browsable deploy (not re-measurable in code).
8. **Minor weak H1s** — `home-inspectors` and `asset-verification` (outside the scope of must-fix #8).

---

## 5. Conclusion

The previous version of the audit (**June 19**) was valid. The team implemented the technical fixes correctly. The "additional errors" from the July 1 review came from **auditing an environment with stale code** (`dev.vlx.ai`); the actual branch code is in good shape. The real focus remains on: **paid landers (blocker)** and **cleanup of blog content in the CMS**.
