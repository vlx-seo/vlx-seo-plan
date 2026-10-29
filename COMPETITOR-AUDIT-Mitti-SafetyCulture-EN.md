# Competitor SEO Audit — Mitti (by SafetyCulture) → Opportunities for VLX

**Competitor:** `mitti.com` (audited via `mitti.com/es`)
**Why it matters:** Mitti is the rebrand of **SafetyCulture / iAuditor** — the market leader in VLX's exact category (digital inspections, checklists, quality, compliance). Its public SEO playbook is, almost line for line, the one VLX should run.

---

## Framing insight

Mitti's organic strength is **not** its homepage — it is a massive **programmatic content library** (topics, checklists, "best software" apps, comparison pages). That library is published **in English only**: the `/es`, `/de`, `/fr`, etc. locales translate the homepage and core marketing pages, but the content engine is not localized. That is a double signal for VLX:

1. The programmatic-content model is the proven engine to copy.
2. The lack of Spanish-language technical content is a gap VLX can exploit.

---

## What Mitti is doing (measured evidence)

### 1. Programmatic content at scale — the core of their SEO
Live URL counts pulled from their segmented sitemaps:

| Content type | URL pattern | Live URLs | What each page is |
|---|---|---:|---|
| Topics | `/topics/{category}/{topic}` | **3,227** | Long-form compliance guides (e.g. "Hazardous substances NZ", ~7,200 words) |
| Checklists | `/checklists/{category}/{slug}` | **1,399** | Downloadable templates (e.g. ISO 9001 review, ~7,300 words) |
| Apps / software | `/apps/{slug}` | **849** | "The N Best X Software of 2026" listicles (~6,000 words) |
| Pages (incl. comparisons) | `/pages` | **766** | Includes `/{competitor}-alternative` pages |
| Training courses | `/training-courses/…` | **433** | Course pages |
| Customers | `/customers/…` | **182** | Case studies |
| Blog | `/blog/…` | **137** | Blog posts |

VLX already owns the right URL namespaces (`/checklists/`, `/use-case`, `/reports`, `/forms`) but at a fraction of this depth. **This is lever #1.**

### 2. Every programmatic asset is long-form + schema-rich (not thin stubs)
- **5,000–8,000+ words** per checklist / topic page.
- Combined structured data: **`Article` + `BreadcrumbList` + `FAQPage`** on nearly every page.
- Repeatable H2 skeleton: *What is… / Importance / What to include / How to use / Sample / FAQ*.
- Title pattern built for CTR + the download magnet: **`Free [X] Template | PDF | Mitti (by SafetyCulture)`**.

### 3. Nested hub-and-spoke taxonomy + heavy internal linking
- Hierarchical URLs with real breadcrumbs: `/checklists/compliance/iso-9001-…`, `/topics/{category}/{topic}`.
- **27–57 internal links per page**, distributing authority across the cluster. This is the direct antidote to the "almost no internal linking" pattern recorded across several VLX blog clusters.

### 4. Bottom-funnel commercial pages that actually convert queries
Example — `/dashpivot-alternative`:
- **8,819 words**, `FAQPage` + `BreadcrumbList` schema, 57 internal links.
- Sections: comparison table, "pricing vs", "why customers choose", construction customer proof, "not sure if it's the right fit".
- Formats: `/{competitor}-alternative` and "The N Best {category} Software of 2026".

This is directly applicable to VLX's commercial pages currently sitting at ~0 clicks: the content exists, the **high-intent structure** does not.

### 5. Global structured data done well
- `Organization` with `logo`, `sameAs` (5 social profiles), `contactPoint`, `foundingDate`, `address`.
- `WebSite` with a **`SearchAction`** (enables the sitelinks search box) and `inLanguage`.
- `WebPage` per URL, all cross-referenced via linked `@id`s.

### 6. Clean, segmented sitemap architecture
A sitemap index split by content type (homepage, pages, topics, checklists, apps, training, customers, blog, …) with fresh `lastmod` timestamps — easy for Google to crawl and prioritize.

### 7. Explicit AI-bot policy in robots.txt (AEO / LLM-visibility play)
`GPTBot`, `ClaudeBot`, `PerplexityBot`, `Google-Extended`, `Applebot-Extended`, `CCBot` are all explicitly `Allow: /` with per-bot `Crawl-delay`. They are deliberately optimizing for citation inside AI answers.

### 8. Correct hreflang on the homepage (7 locales + x-default) — library stays English
The homepage ships a full hreflang set; the content library does not. VLX, with genuine Spanish content, can win Spanish-language SERPs where Mitti only offers English.

### 9. Fast SSR (Next.js) — with a cautionary note
- TTFB ~212 ms, full load ~894 ms, HTTP/2.
- **But:** ~214 requests and **~94 scripts** (GTM, Usercentrics CMP, etc.), and images served **without WebP/AVIF** (0 of 66). Lazy-loading is in place (55/66).
- Copy the SSR speed and lazy-loading; **do not** copy the script bloat or the legacy image formats — VLX can beat them on Core Web Vitals here.

---

## Prioritized recommendations for VLX

**P0 — Scale the checklist/template library into an engine.**
`/checklists/` is already the canonical namespace. Build it out: nested `/checklists/{category}/{slug}` taxonomy, each page 2,000–4,000+ words, `Article` + `FAQPage` + `BreadcrumbList`, a downloadable PDF template, and the title pattern `Free [X] Template | PDF | Visualogyx`.

**P0 — Add a "best X software" / "X alternative" commercial layer.**
Build the equivalent of `/apps` for the commercial queries currently at ~0 clicks: "best inspection software", "{competitor} alternative", with a comparison table + FAQ schema + pricing section. Proven format, high intent.

**P1 — Hub-and-spoke internal linking + breadcrumbs across every cluster.**
Directly fixes the recurring "almost no internal linking" problem. Ship `BreadcrumbList` schema with it.

**P1 — Complete global structured data.**
`WebSite` schema with `SearchAction` and a full `Organization` block (sameAs, logo, contactPoint) if not already present at this level.

**P2 — Deliberate AI-bot policy in robots.txt.**
Decide VLX's stance on `GPTBot` / `ClaudeBot` / `PerplexityBot`, aligned with the reality of the ALB bot handling — intentional, not accidental.

**P2 — Spanish-language content advantage.**
Publish technical/compliance content in Spanish where Mitti only has English.

**Do NOT copy:** the ~94-script load or the non-WebP/AVIF images. Maintaining performance discipline is where VLX can out-rank them on page experience.

---

## Quick wins
Low effort, no new content production, shippable in days — mostly template/schema changes applied to pages VLX already has.

| # | Quick win | Effort | Impact |
|---|---|---|---|
| QW1 | Add `WebSite` schema with `SearchAction` + complete the `Organization` block (logo, sameAs, contactPoint, foundingDate) — one global change | Low | Sitelinks search box + richer brand entity |
| QW2 | Add `FAQPage` + `BreadcrumbList` schema to existing `/checklists/` and key blog pages | Low | Rich results / SERP real estate |
| QW3 | Reformat existing checklist titles to the proven pattern `Free [X] Template \| PDF \| Visualogyx` | Low | Higher CTR on template queries |
| QW4 | Add contextual internal links + visible breadcrumbs inside existing clusters (fixes the recurring "almost no internal linking") | Low | Authority distribution, crawl depth |
| QW5 | Decide and set the AI-bot policy in `robots.txt` (GPTBot / ClaudeBot / PerplexityBot), aligned with the ALB bot handling | Low | Deliberate AEO / LLM-citation posture |
| QW6 | Reshape the ~0-click commercial pages into the comparison template (table + pricing + FAQ) — reuse existing copy, no net-new research | Medium | Convert high-intent queries already targeted |
| QW7 | Serve WebP/AVIF + confirm lazy-loading on existing images | Low | Core Web Vitals / LCP, beats Mitti on page experience |

---

## Work plan (phased roadmap)
Owners are placeholders — assign in the kickoff. Each phase is independently shippable.

### Phase 0 — Baseline & quick wins (Week 1–2)
- **Size the gap:** crawl VLX's own sitemaps and count current checklist / app / comparison URLs vs. Mitti's 1,399 / 849 / 766 to quantify the backlog with real numbers.
- Ship **QW1–QW5 and QW7** (schema, titles, internal linking, robots, images).
- Define the **page templates** (checklist, "best software", comparison) with the required H2 skeleton and schema baked in, so future pages are consistent by default.
- *Exit:* measured gap + reusable templates + schema/linking fixes live.

### Phase 1 — Checklist/template engine (Week 3–8)
- Build the **nested taxonomy** `/checklists/{category}/{slug}` with breadcrumbs.
- Produce a first batch (target ~20–30 pages) of **long-form checklist pages** (2,000–4,000+ words) each with a downloadable PDF template, prioritized by the keyword data already in the Ranki/GSC sheet.
- Interlink every new page into its category hub (hub-and-spoke).
- *Exit:* repeatable production pipeline + first batch indexed.

### Phase 2 — Commercial / bottom-funnel layer (Week 6–10, overlaps Phase 1)
- Ship **QW6** plus net-new **"best X software"** listicles and **"{competitor}-alternative"** pages for VLX's top commercial targets.
- Comparison table + pricing section + `FAQPage` schema on each.
- *Exit:* the 0-click commercial queries covered by the proven format.

### Phase 3 — Scale + Spanish advantage (Week 10+)
- Scale checklist/topic production in batches toward a meaningful share of Mitti's footprint (prioritize by volume × intent, not raw count).
- Publish **Spanish-language** technical/compliance content where Mitti only offers English — a SERP VLX can own.
- Add **topic hub guides** (`/topics/{category}/{topic}` equivalent) around the service clusters.
- *Exit:* compounding content moat + ES coverage.

### Ongoing
- Measure in GSC per cluster; re-optimize underperformers.
- Keep sitemaps segmented with fresh `lastmod`.
- Maintain performance discipline (don't inherit Mitti's script bloat).
