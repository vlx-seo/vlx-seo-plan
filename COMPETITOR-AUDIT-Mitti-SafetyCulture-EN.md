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

## Suggested next step
Crawl VLX's own sitemaps and quantify the exact gap (current VLX checklist / app / comparison counts vs. Mitti's 1,399 / 849 / 766) to size the content-production backlog with real numbers.
