# SEO On-Page / Technical Audit — VLX Home

- **Audited URL:** `https://dev.vlx.ai/vlx-home/`
- **Date:** 2026-10-01
- **Environment:** DEV (behind Cognito login). Accessed via an authenticated browser session.
- **Author:** lceballos-seo
- **Scope:** single-page on-page and technical audit of the "Early Access" landing page.

---

## 0. Context and page state

The page currently live at `/vlx-home/` is **not** the version previously documented (the "home inspection business software" build, ~1,282 words, Start Free Trial / Book a Demo CTAs, at `/digital-inspections-software/home-inspection/`). It is a **new "Early Access" landing rebuild**:

- **Title:** `VLX Home: Residential Inspection Software | VLX`
- **H1:** `The AI-powered home inspection platform.`
- **Single CTA:** `Request Early Access` (consistent with no self-serve signup yet — honest).
- **Positioning:** residential inspectors, AI (AI Walkthrough / AI Scribe / AI Template Builder), branded reports, booking/scheduling, pricing $69–$75, FAQ.
- **New route:** root `/vlx-home/` (previously level 3 under `/digital-inspections-software/`).
- **Length:** 884 words.

**What is already GOOD** (leave as is): a single H1, clean heading hierarchy with no skipped levels, 5 valid structured-data blocks, hreflang paired with the canonical, correct viewport and `lang`, decorative icons correctly using `alt=""`, strong conversion copy, internal linking to sibling pages.

> **Important note on `noindex,nofollow`:** the page carries it **and that is correct on DEV** (pre-launch rule). It is not a bug. But it is the reason none of the ranking improvements take effect until launch → see P0-1.

---

## Priority summary

**P0 — ranking blockers (do before/at launch)**
1. Flip to `index,follow` in production (`APP_ENV` gate).
2. Keyword coverage: "home inspection" currently appears **2 times in 884 words** (~0.2%). Insufficient.
3. Pick and validate **one** primary keyword and align title ↔ H1 ↔ meta ↔ H2 (and resolve cannibalization with `/home-inspectors/`).

**P1 — high impact**
4. Hero image uses `loading="lazy"` → the LCP image must not be lazy-loaded (hurts LCP).
5. Add secondary keywords to 2-3 H2s.
6. Rewrite the title (keyword first, no duplicated brand).
7. Meta description with the primary keyword near the front.
8. Add `offers` (price) to the `SoftwareApplication` schema.
9. Decide URL architecture (`/vlx-home/` vs `/digital-inspections-software/app/vlx-home/`).

**P2 — improvements**
10. More descriptive hero `alt`.
11. Verify `og:image` uses an absolute PROD URL at launch.
12. Add a comparison link (`/vs-spectora`) — Spectora is already mentioned in pricing.
13. Validate all 5 schemas in Rich Results Test after launch.

**P3 — minor / optional**
14. Simplify the hreflang set.
15. Measure real CWV (PSI/CrUX) and TTFB at launch.

---

## 1. On-page SEO

### 1.1 Title tag — `P1`
**Current:** `VLX Home: Residential Inspection Software | VLX` (47 characters).

**What to improve:**
- Length is fine (47 chars), but **the brand appears twice** ("VLX Home:" at the start and "| VLX" at the end) → wastes pixels and pushes the keyword to the middle.
- It starts with the brand, not the keyword. Keyword should sit as early as possible.
- It targets **"residential inspection software"**, a term whose volume **could not be verified** (Ranki at 0 credits). Prior research does have data for "home inspection software" / "home inspector software" (≈1,300/mo each, difficulty 20-32).

**How to do it** (depending on the keyword decision, see §4.1). Two clean options:
- If the primary is *home inspection software* (differentiated from `/home-inspectors/` by the residential/AI angle):
  `Home Inspection Software for Residential Inspectors | VLX Home` (59 chars)
- If keeping *residential inspection software* (requires volume validation):
  `Residential Inspection Software with AI | VLX Home` (50 chars)

In both cases: keyword first, brand once at the end. In Next.js use `title.absolute` so it doesn't inherit the root layout's "| VLX" and you control the suffix manually.

### 1.2 Title ↔ H1 mismatch — `P0`
- **Title:** "Residential Inspection **Software**"
- **H1:** "The AI-powered home inspection **platform**."

These are two different heads ("residential inspection software" vs "home inspection platform"). Google needs a **consistent** keyword signal.

**How to do it:** fix ONE primary keyword and make it appear — in the same form — in: title, H1, first paragraph (first 100 words), at least one H2, meta description, and the hero `alt`. Example H1 aligned if the primary is *home inspection software*:
`AI-powered home inspection software for residential inspectors` (keeps the AI hook and includes the keyword).

### 1.3 Meta description — `P1`
**Current** (149 chars, good length):
> "VLX Home runs the whole inspection business: online booking, scheduling, and job management, plus the AI walkthrough that drafts your report on site."

**What to improve:** it does not contain the exact primary keyword (neither "residential inspection software" nor "home inspection software"). Google bolds it when it matches the query → helps CTR.

**How to do it** (≈150 chars, keyword first + CTA):
> "Home inspection software built for residential inspectors: online booking, scheduling, AI walkthrough and branded reports. Now in Early Access — request a seat."

### 1.4 Headings (H1–H3) — `P1`
**State:** 1 H1, correct hierarchy (H1→H2→H3, no skips), 23 headings. **The structure is good.**

**What to improve:** the H2s are 100% benefit-driven and **carry almost no keywords**:
- "Spend less time writing reports…", "AI that works with you.", "Reports that build your reputation.", "Get your evenings back.", "From booking to report, VLX Home keeps it moving.", "Better value. Simple pricing.", "Questions inspectors ask."

Only 1 H2 contains "VLX Home" and 0 contain "home inspection". You can keep the tone while **adding** the keyword:

**How to do it** (rewrites that keep the tone):
- "Reports that build your reputation." → **"Home inspection reports that build your reputation."**
- "From booking to report, VLX Home keeps it moving." → **"How VLX Home inspection software works, from booking to report."**
- "AI that works with you." → **"AI home inspection tools that work with you."**

Two or three H2s like this cover the secondary keyword without over-optimizing.

### 1.5 Body keyword coverage / density — `P0`
**Hard data:** across 884 words —
- "home inspection" → **2 times**
- "inspection software" → **1 time**
- "residential" → 2 times
- "AI" → 18 times
- "VLX Home" → 7 times

The page is **optimized for AI and for conversion, but under-optimized for the inspection-software term**. Primary density ≈0.2%.

**How to do it:**
- Raise the primary keyword to a **natural 4-6 uses** (target ~1-1.5% density, no stuffing).
- Ensure it appears in: first 100 words, one H2, one feature description, the hero `alt`, and the FAQ block.
- Natural places to insert it: the paragraph under the H1, the "How VLX Home works" section (currently "VLX Home keeps it moving" → "…the home inspection software keeps it moving"), and the "What is VLX Home?" FAQ answer.

### 1.6 Content length and depth — `P2` (strategic)
884 words is reasonable for a focused Early Access landing, but it is **thinner** than what typically ranks for competitive software terms and than the prior build (1,282 words).

**Decision for Laura:** does this page need to *rank* for a head term, or is it mainly an Early Access lead capture?
- If it must rank → add supporting content without breaking the flow: a **supported residential inspection types** block (full home, wind mitigation, 4-point, radon, roof…) and an **integrations** block, both keyword-rich. (These existed in the prior version and can be recovered.)
- If it is lead capture only → keep the current length and accept that traffic will come from campaigns, not organic.

---

## 2. Images

### 2.1 Hero image lazy-loaded — `P1` (LCP impact)
The large hero image (1395×278, `alt="VLX Home"`) is set to **`loading="lazy"` with no `fetchpriority`**. If it is the LCP element (typical for a hero), lazy-loading it **delays LCP** — the opposite of what you want.

**How to do it:**
- Hero / LCP image: `loading="eager"` + `fetchpriority="high"` (remove `lazy`).
- In `next/image`: use `priority` (equals eager + high priority) on the hero image; **never** `priority` on below-the-fold images.
- Other below-the-fold images: keep `lazy` (that is fine).

### 2.2 Hero alt — `P2`
`alt="VLX Home"` is valid but generic. Use it for a natural keyword:
`alt="VLX Home home inspection software — example client report"`.
(The logo with `alt="VLX"` is fine. The 32 decorative icons with `alt=""` are **correct**, not a defect.)

---

## 3. Technical SEO

### 3.1 Indexability — `P0` (launch checklist)
- `robots: noindex, nofollow` → correct on DEV. **Launch item #1 is that PROD serves `index, follow`** via the `APP_ENV` gate (otherwise the page never enters Google).
- At launch, verify the PROD URL returns 200 + `index,follow` and is in the sitemap.

### 3.2 Canonical — OK
`canonical → https://vlx.ai/vlx-home/` (points to the PROD URL, self-referential). Correct. Just confirm the final PROD route matches exactly (see §4.2 on architecture).

### 3.3 Hreflang — OK (optional improvement `P3`)
6 entries (en-US, en-CA, en-CO, en-MX, en, x-default) **all pointing to the same URL** and matching the canonical → passes the `verify-seo-invariants.js` guard and is technically valid.

**Optional:** pointing 5 locales to an identical URL adds no real signal; it could be reduced to `en` + `x-default`. Not a bug; leave as is if the build guard requires it.

### 3.4 Structured data — OK with one improvement `P1`
5 valid JSON-LD blocks: **Organization, WebSite, BreadcrumbList, SoftwareApplication, FAQPage** (FAQ with 6 Q&A). Excellent coverage.

- `SoftwareApplication`: `BusinessApplication`, OS `Web, iOS, Android`. **Missing `offers`** despite the public price ($69/mo annual, $75 month-to-month).
  **How to do it:** add `offers` with `price`, `priceCurrency: "USD"`, `category`. Improves rich-result eligibility.
- **`aggregateRating`: absent — and it should stay that way.** The page shows Capterra 4.9 / G2 4.8 and testimonials, but those ratings are for the **VLX product overall, not VLX Home**. Adding `aggregateRating` here would be a review rich result about a non-matching object → risk of a Google manual action. **Do not add** until there are VLX Home-specific reviews.
- Validate all 5 blocks in **Rich Results Test** after launch (on DEV with noindex they do not show rich results).

### 3.5 Open Graph / social — `P2`
8 OG tags + `twitter:card = summary_large_image`. Good. Two details:
- **`og:image` points to `dev.vlx.ai/...`** (DEV domain). Next.js usually regenerates it per environment, but **verify at launch** that the OG image uses the absolute PROD URL (`vlx.ai`).
- **Missing `og:type`** (should be `website`). Add it.

### 3.6 Performance / Core Web Vitals — `P1`/`P3`
- **The lazy hero (§2.1) is the actionable item right now** for LCP.
- Measured TTFB ≈480 ms, but with a warm cache and `transferSize=0` (cached resources) → **not a reliable field measurement**. 23 scripts and 9 stylesheets loaded.
- **How to do it:** at launch, measure with **PageSpeed Insights / CrUX** (field data) for real LCP/INP/CLS and TTFB. Remember the site's INP already hovers around 200 ms on mobile (historical) → watch that this page's interactive components (job track, FAQ accordion) do not worsen INP.

### 3.7 Mobile / accessibility — OK
Correct viewport, `lang="en"`, VLX system responsive design. No mobile-first findings. (Confirm tap targets ≥48px on the Early Access form in a real test.)

---

## 4. Architecture and keyword strategy

### 4.1 Cannibalization with `/home-inspectors/` — `P0` (decision)
The **keyword ownership contract** reserves "home inspection software" for `/home-inspectors/`. This page:
- uses "Residential Inspection Software" in the title and "home inspection platform" in the H1, and
- links to `/home-inspectors/` as *"Home inspection software for firms"* → **a deliberate differentiation signal** (good).

**What to decide and how:** explicitly set the split:
- `/vlx-home/` = **residential + AI + Early Access** (solo inspector / small business).
- `/home-inspectors/` = **firms / companies** (keeps "home inspection software").

Once set, align title/H1/meta/H2 of `/vlx-home/` to its primary and **make sure it does not repeat the exact head of `/home-inspectors/`**. Validate the volume of "residential inspection software" in Ahrefs/Ranki before freezing the title (not verified today).

### 4.2 URL architecture — `P1` (decision)
The page lives at **root `/vlx-home/`**. The site's own Search Console data (historical) shows that **level-3 URLs under `/digital-inspections-software/app/`** rank far better (COUNTiT pos ≈5.2; KYPiT pos ≈2.2) than level 1-2 paths (`/product/` pos 27-56). The prior recommendation was `/digital-inspections-software/app/vlx-home/`.

**Trade-off to resolve with Laura:**
- *For root `/vlx-home/`*: short, memorable URL for an Early Access campaign; easy to communicate.
- *For level 3*: inherits the topical authority of the hub that already ranks; consistent with the siblings (KYPiT/COUNTiT).

**How to do it:** if the page's goal is mid-term organic, move it to `/digital-inspections-software/app/vlx-home/` (or at least hang it off the hub) and update canonical + breadcrumb. If it is purely a lead campaign, root is acceptable. **Decide before launch** to avoid a 301 later.

### 4.3 Internal linking — OK (improvement `P2`)
84 internal links (most from the global nav/footer of the layout). The page links to KYPiT, COUNTiT, `/home-inspectors/`, platform overview, and inspection-companies. Good linking.

**Improvement:** the pricing compares with **Spectora** ($109) but **there is no link to a `/vs-spectora` comparison** (nor `/vs-homegauge`). Research shows the real volume is in competitor brands (Spectora 9,900/mo) → conquesting is the lever. Add a contextual link to the comparison (once it exists) from the pricing block.

---

## 5. Launch checklist (actionable summary)

1. [ ] PROD serves `index, follow` (`APP_ENV` gate) and 200.
2. [ ] Primary keyword fixed and validated; title/H1/meta/H2/alt aligned.
3. [ ] Primary keyword density raised to 4-6 natural uses.
4. [ ] Hero/LCP: `priority` (eager + fetchpriority high), not lazy.
5. [ ] `SoftwareApplication.offers` with price; `aggregateRating` **left out**.
6. [ ] `og:type` added; `og:image` with absolute PROD URL verified.
7. [ ] URL architecture decided (root vs level 3) — avoid a later 301.
8. [ ] Keyword split with `/home-inspectors/` confirmed (anti-cannibalization).
9. [ ] Validate all 5 schemas in Rich Results Test.
10. [ ] Measure real CWV in PSI/CrUX; watch job track + FAQ INP.
11. [ ] (Optional) link to a `/vs-spectora` comparison.
12. [ ] Confirm the URL in the sitemap and request indexing in GSC.

---

*Audit performed on the DEV version as of 2026-10-01. Keyword volumes could not be re-verified live (Ranki API at 0 credits); they rely on previously documented research. Re-validate in Ahrefs/Ranki before freezing the title.*
