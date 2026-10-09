# SEO On-Page / Technical Audit — VLX Home

- **Audited URL:** `https://dev.vlx.ai/vlx-home/`
- **Date:** 2026-10-09 (rev. 5 — decided URL updated to `/home-inspection-software/`)
- **Environment:** DEV (behind Cognito login). Accessed via an authenticated browser session.
- **Author:** lceballos-seo
- **Scope:** single-page on-page and technical audit of the "Early Access" landing page.

> **Primary keyword (defined):** **`home inspection software`**. All on-page signals must align to this term. The page must **not** target "residential inspection software" (which is what the title targets today).

---

## 0. Context and page state

The page currently live at `/vlx-home/` is **not** the version previously documented (the build with the sub-service hub and the defined keyword map, at `/digital-inspections-software/home-inspection/`). It is a **new "Early Access" landing rebuild**:

- **Title:** `VLX Home: Residential Inspection Software | VLX` ← **targets the wrong keyword**
- **H1:** `The AI-powered home inspection platform.`
- **Single CTA:** `Request Early Access`.
- **Length:** 884 words. It does **not** include the inspection-types hub or the integrations block the prior version had.
- **New route:** root `/vlx-home/` (previously level 3 under `/digital-inspections-software/`).

**The underlying error:** this rebuild **lost the keyword architecture we had already defined** (primary keyword + secondaries + a sub-service hub with its 9 specialties). It must be reinstated. See §1 (keyword map).

**What is already GOOD** (leave as is): a single H1, clean heading hierarchy with no skipped levels, 5 valid structured-data blocks, hreflang paired with the canonical, correct viewport and `lang`, decorative icons correctly using `alt=""`, strong conversion copy, internal linking to sibling pages.

> **Note on `noindex,nofollow`:** the page carries it **and that is correct on DEV** (pre-launch rule). It is not a bug, but it is the reason none of the ranking improvements take effect until launch → see P0-1.

---

## Priority summary

**P0 — blockers (do before/at launch)**
1. Flip to `index,follow` in production (`APP_ENV` gate).
2. **Fix the primary keyword to `home inspection software`** and align title ↔ H1 ↔ meta ↔ H2 ↔ alt ↔ body. Today the title targets "residential inspection software" (wrong keyword).
3. **Reinstate the defined keyword map** (primary + secondaries + sub-service hub) — see §1.
4. Keyword coverage: "home inspection" appears **2 times in 884 words** (~0.2%). Insufficient.
5. Resolve cannibalization with `/home-inspectors/` (Option A: VLX Home keeps "home inspection software", `/home-inspectors/` is re-focused).
6. **Change the URL** from `https://vlx.ai/vlx-home/` to **`https://vlx.ai/home-inspection-software/`** (+ 301 + canonical + og:url) — see §5.2.

**P1 — high impact**
6. Hero image uses `loading="lazy"` → the LCP image must not be lazy.
7. Rewrite the title (primary keyword first, no duplicated brand).
8. Meta description with the primary keyword near the front.
9. Add secondaries to 2-3 H2s.
10. Recover the sub-service hub (9 specialties, inert "Page coming" links) — the vehicle for the long-tail keywords.
11. Add `offers` (price) to the `SoftwareApplication` schema.
12. Add a **visible breadcrumb** (today only the schema exists, not the element).

**P2 — improvements**
13. Hero `alt` with the primary keyword.
14. Verify `og:image` uses an absolute PROD URL; add `og:type`.
15. Add a comparison link (`/vs-spectora`).
16. Validate all 5 schemas in Rich Results Test.

**P3 — minor / optional**
17. Simplify the hreflang set.
18. Measure real CWV (PSI/CrUX) and TTFB.

---

## 1. Defined keyword map (to implement) — `P0`

This is the keyword architecture we had already closed (source: `VLX-Home-subpaginas-keywords.csv` + brief). **The current rebuild does not reflect it.**

### 1.1 VLX Home page (core)
- **Primary keyword:** `home inspection software`
- **Secondaries (from CSV):** `home inspection business software`, `home inspection app`, `mobile home inspection software`
- **Supporting secondaries (brief):** `home inspection report software`, `inspection scheduling software`, `inspection invoicing software`, `offline inspection app`, `photo authentication software`

**Where to place them:**
- `home inspection software` → title (first), H1, first paragraph (first 100 words), ≥1 H2, hero alt, "What is VLX Home?" FAQ answer.
- `home inspection app` / `mobile home inspection software` → mobile app / field-capture section (today "Software that slows you down in the field" and the AI block).
- `inspection scheduling software` → booking/scheduling section ("Clients book online" / "Your team stays organized").
- `home inspection report software` → "Reports…" section.
- `inspection invoicing software` → pricing block ("Invoicing and customer payments — COMING SOON").
- `offline inspection app` / `photo authentication software` → AI/field block (KYPiT = photo authentication is the moat; mention it).

### 1.2 Sub-service hub (9 specialties) — recover
The rebuild **removed** the inspection-types hub. It must be rebuilt as a card section; each specialty is an **inert** link ("Page coming") that projects its future sub-page and anchors its keyword. Closed list:

| Sub-service | Primary keyword | Anchor | Provisional URL |
|---|---|---|---|
| Roof inspection | `roof inspection software` | Explore roof inspection software | `/home-inspection-software/roof/` |
| Wind mitigation | `wind mitigation inspection software` | Explore wind mitigation software | `/home-inspection-software/wind-mitigation/` |
| 4-point inspection | `4-point inspection software` | Explore 4-point inspection software | `/home-inspection-software/4-point/` |
| Radon testing | `radon inspection software` | Explore radon inspection software | `/home-inspection-software/radon/` |
| Mold inspection | `mold inspection software` | Explore mold inspection software | `/home-inspection-software/mold/` |
| Sewer scope | `sewer scope inspection software` | Explore sewer scope software | `/home-inspection-software/sewer-scope/` |
| Pool & spa | `pool inspection software` | Explore pool inspection software | `/home-inspection-software/pool/` |
| Termite / WDO | `termite inspection software` | Explore termite / WDO software | `/home-inspection-software/termite-wdo/` |
| New construction & pre-purchase | `new construction inspection software` | Explore new construction software | `/home-inspection-software/new-construction/` |

Each card carries its keyword in the **title, description and anchor** ("Explore <kw> →"). Inert links (`data-href`, `aria-disabled`, "Page coming" badge) until the sub-pages exist. The compliance forms (OIR-B1-1802, Citizens 4-Point, TREC REI 7-6) already appear in the reports section → good, reinforce with the CSV secondaries.

> **Architecture (resolved):** the core page lives at **`/home-inspection-software/`** (§5.2) and the 9 sub-pages nest under it (`/home-inspection-software/<specialty>/`). Coherent silo core → specialties, additionally linked from the hub `/digital-inspections-software/`.

---

## 1-bis. FULL guideline check — LIVE (2026-10-02)

Measured against the live page with focus keyword **`home inspection software`**. Legend: ✅ pass · ❌ fail · ⚠️ weak/risk · 🟡 adapt · ⚪ optional.

### A. Basic SEO
| Rank Math test | Result | Measured detail |
|---|---|---|
| Focus KW in **Title** | ❌ FAIL | title = "residential inspection software" (wrong keyword) |
| Focus KW in **Meta description** | ❌ FAIL | meta contains neither the exact phrase nor the partial "home inspection" |
| Focus KW in **URL** | ❌ FAIL (today) | live `/vlx-home/` does not contain it; the **decided** URL `/home-inspection-software/` does (EMU) → will pass once implemented (§5.2) |
| Focus KW in **first 10%** | ❌ FAIL | first exact appearance at **87.7%**; only the partial "home inspection" in the first 10% (H1) |
| Focus KW in **Content** | ⚠️ WEAK | exact phrase **1 time** in the whole body |
| **Content Length** | 🟡 | **884 words** (acceptable for a landing; ideal 900–1,300) |

### B. Additional SEO
| Test | Result | Measured detail |
|---|---|---|
| Focus KW in **Subheadings (H2/H3)** | ❌ FAIL | **0 of 9 H2s** and **0 H3s** contain the phrase (not even the partial) |
| Focus KW in **Image Alt** | ❌ FAIL | **0 alts** with the exact phrase; the 8 content images have descriptive alts but no focus keyword |
| **Keyword Density** | ❌ FAIL | primary **0.11%** (target 1–1.5%); secondaries 0% |
| **URL Length ≤75** | ✅ PASS | short |
| **Linking to Internal Resources** | ✅ PASS | 81 internal links (hub, `/home-inspectors/`, apps, pricing) |
| **Linking to External Sources** | ✅ PASS | 17 external links (Capterra, G2, NMSDC, socials) |
| **External link DoFollow** | ✅ PASS | all 17 external are dofollow |
| **Focus KW Uniqueness** | ⚠️ RISK | "home inspection software" is currently owned by `/home-inspectors/` → §5.1 |

### C. Title Readability
| Test | Result | Detail |
|---|---|---|
| Focus KW at **start of Title** | ❌ FAIL | title starts with the brand and the wrong keyword |
| Sentiment / Power word / Number in title | ⚪ OPTIONAL | not critical for a product page |

### D. Content Readability
| Test | Result | Measured detail |
|---|---|---|
| **Short Paragraphs** (<120 words) | ✅ PASS | longest paragraph = 38 words (105 `<p>`) |
| **Use of Media** (≥4) | ✅ PASS | 8 content images (46 `<img>` incl. icons) |
| **Table of Contents** | ⚪ OPTIONAL | short landing, not needed |

### E. Coverage of the DEFINED secondary keywords (the 8 in map §1.1)
❌ **FAIL — all 8 appear 0 times as an exact phrase:** `home inspection business software` (0), `home inspection app` (0), `mobile home inspection software` (0), `home inspection report software` (0), `inspection scheduling software` (0), `inspection invoicing software` (0), `offline inspection app` (0), `photo authentication software` (0). The loose concepts exist (booking ×3, scheduling ×2, invoicing ×1, report ×16) but **no secondary keyword is built as such**. "offline" and "photo authentication"/KYPiT: **0 mentions**.

### F. Sub-service hub (the 9 specialties §1.2)
❌ **FAIL — the hub does not exist in this version.** Only `wind mitigation` (1) and `4-point` (1) are mentioned in the forms line. `roof`, `radon`, `mold`, `sewer scope`, `pool`, `termite/WDO`, `new construction` = **0 mentions**.

### G. Technical (summary; detail in §4)
| Item | Result |
|---|---|
| `robots` | ✅ `noindex,nofollow` correct on DEV (flip in prod = P0) |
| Canonical | ⚠️ points to `/vlx-home/` → **must change** (see §5.2 / URL point) |
| Hreflang | ✅ 6 entries paired with the canonical |
| Schema | ✅ 5 blocks (Organization, WebSite, BreadcrumbList, SoftwareApplication, FAQPage) |
| **Visible** breadcrumb | ❌ BreadcrumbList schema present but **no** visible breadcrumb on the page → add it (Home › Home Inspection Software) |
| `og:type` | ❌ absent |
| `SoftwareApplication.offers` | ❌ absent (public price) |

**Verdict:** with the correct keyword, the page **fails the entire Basic SEO block + the Additional KW checks + the full coverage of secondaries and sub-services**. It passes readability, linking and media use. The 3 you asked about (image ❌ · H2 ❌ · first 10% ❌) fail. The "how" is in §1 (map) and §2 (on-page).

---

## 2. On-page SEO

### 2.1 Title tag — `P0`
**Current:** `VLX Home: Residential Inspection Software | VLX` (47 chars) → **targets "residential inspection software", which is NOT the target keyword.**

**What to improve:**
- Change to the primary keyword **`home inspection software`**.
- Put it first; brand once at the end (today "VLX Home:" + "| VLX" duplicates the brand).

**How to do it** (options, 50-60 chars):
- `Home Inspection Software for Inspectors | VLX Home` (50 chars)
- `AI Home Inspection Software | VLX Home` (38 chars — leaves room and keeps the AI hook)
- `Home Inspection Software & App | VLX Home` (41 chars — adds the "app" secondary)

In Next.js: use `title.absolute` to control the suffix and not inherit "| VLX" from the root layout.

### 2.2 Title ↔ H1 mismatch — `P0`
- **Title (today):** "Residential Inspection **Software**"
- **H1 (today):** "The AI-powered home inspection **platform**."

They match neither each other nor the target keyword.

**How to do it:** make `home inspection software` appear — in the same form — in title, H1, first paragraph, ≥1 H2, meta and hero alt. Proposed H1:
`AI-powered home inspection software for inspectors` (keeps AI + adds the exact keyword).

### 2.3 Meta description — `P1`
**Current** (149 chars): *"VLX Home runs the whole inspection business: online booking, scheduling, and job management, plus the AI walkthrough that drafts your report on site."* → does not contain the exact keyword.

**How to do it** (≈150 chars, keyword first + CTA):
> "Home inspection software built for inspectors: online booking, scheduling, AI walkthrough and branded reports. Now in Early Access — request a seat."

### 2.4 Headings (H1–H3) — `P1`
Correct structure (1 H1, no skips, 23 headings). The H2s are benefit-only and **carry no keywords**. Rewrites that keep the tone and add the secondaries:
- "Reports that build your reputation." → **"Home inspection report software that builds your reputation."**
- "From booking to report, VLX Home keeps it moving." → **"How VLX Home inspection software works, from booking to report."**
- "AI that works with you." → **"AI home inspection tools that work with you."**
- "Software that slows you down in the field" → consider adding "mobile home inspection app" to the H3/description.

### 2.5 Coverage / density — `P0`
Across 884 words: "home inspection" **2x**, "inspection software" **1x**, "AI" 18x, "VLX Home" 7x. Primary density ≈0.2%.

**How to do it:** raise `home inspection software` to **4-6 natural uses** (~1-1.5%) across: first 100 words, one H2, one feature description, hero alt and the FAQ. Insert the secondaries where they belong (see §1.1). No stuffing.

### 2.6 Length / depth — `P2` (strategic)
884 words is thin for a competitive software term. Recovering the **sub-service hub** (§1.2) and the **integrations block** improves not just conversion but word count and semantic coverage. Decide with Laura whether the page must rank organically (→ recover those blocks) or is purely lead capture.

---

## 3. Images

### 3.1 Hero image lazy-loaded — `P1` (LCP impact)
Hero 1395×278 (`alt="VLX Home"`) with **`loading="lazy"` and no `fetchpriority`**. If it is the LCP, lazy delays it.
**How:** hero/LCP → `loading="eager"` + `fetchpriority="high"` (in `next/image`, `priority`). Others below the fold, `lazy` is fine.

### 3.2 Hero alt — `P2`
`alt="VLX Home"` → `alt="VLX Home home inspection software — example client report"`. The 32 decorative icons with `alt=""` are **correct**.

---

## 4. Technical SEO

### 4.1 Indexability — `P0`
`noindex,nofollow` correct on DEV. **Launch item #1:** PROD must serve `index,follow` (`APP_ENV` gate) + 200 + be in the sitemap.

### 4.2 Canonical — `P0` (must change with the URL)
Today `canonical → https://vlx.ai/vlx-home/`. It is self-referential, but it points to the **wrong URL**: it must become **`https://vlx.ai/home-inspection-software/`** together with the route change (§5.2). Same for `og:url`.

### 4.2-bis Visible breadcrumb — `P1`
There is a `BreadcrumbList` in the schema **but no visible breadcrumb** on the page. Add the visible breadcrumb (Home › Digital Inspections Software › Home Inspection) to reinforce the silo and match the schema.

### 4.3 Hreflang — OK (optional improvement `P3`)
6 entries (en-US/en-CA/en-CO/en-MX/en/x-default) → same URL = canonical. Valid (passes the guard). Optional: reduce to `en` + `x-default`.

### 4.4 Structured data — OK with one improvement `P1`
5 valid blocks (Organization, WebSite, BreadcrumbList, SoftwareApplication, FAQPage; FAQ 6 Q&A).
- `SoftwareApplication`: **missing `offers`** despite a public price ($69/$75). Add `offers` with `price`, `priceCurrency: "USD"`, `category`.
- `aggregateRating`: **absent and should stay out** — the Capterra/G2 ratings are for the VLX product overall, not VLX Home; adding them here would be a rich result about a non-matching object (manual-action risk).
- Validate in Rich Results Test after launch.

### 4.5 Open Graph / social — `P2`
8 OG + `twitter:card=summary_large_image`. **Missing `og:type`** (set `website`). **`og:image` points to `dev.vlx.ai`** → verify the absolute PROD URL at launch.

### 4.6 Performance / CWV — `P1`/`P3`
The lazy hero (§3.1) is the actionable item now. TTFB ≈480 ms measured with a warm cache (not reliable). 23 scripts, 9 CSS. Measure with PSI/CrUX at launch; watch INP of the interactive components (the site's mobile baseline hovers around 200 ms).

### 4.7 Mobile / accessibility — OK
Viewport, `lang="en"`, VLX system responsive. Confirm tap targets ≥48px on the Early Access form.

---

## 5. Architecture and strategy

### 5.1 Cannibalization with `/home-inspectors/` — `P0` (active decision: Option A)
VLX Home's primary keyword is `home inspection software`. That term **is currently ranked by `/home-inspectors/`**. To avoid internal competition:
- **Option A (the active one):** VLX Home keeps `home inspection software`; **re-focus `/home-inspectors/`** onto a different primary (e.g. `home inspection report software` or "…for firms/companies").
- Until `/home-inspectors/` is re-focused, there is a cannibalization risk. This re-focus is a **prerequisite** for launching VLX Home on this keyword.

### 5.2 URL architecture — `P0` (DECIDED, updated 2026-10-09)
**The URL must NOT be `https://vlx.ai/vlx-home/`.** It must be:

> **`https://vlx.ai/home-inspection-software/`**

**Why:**
- **Exact keyword in the slug (EMU):** the URL contains the full primary keyword `home-inspection-software` → **it satisfies the "Focus KW in URL" check** (today ❌) with the strongest possible signal.
- **Reinforces Option A:** a URL that literally claims "home inspection software" consolidates VLX Home as the owner of the head term (and requires re-focusing `/home-inspectors/`, §5.1).
- **Short and clean:** 1 level, readable, easy to communicate and link to.
- **Accepted trade-off:** being a root URL, it does not hang off the `/digital-inspections-software/` hub, so it does not inherit the silo's authority; this is offset by the exact slug + inbound internal links from the hub and sibling pages.

**How to do it:**
1. Serve the page at `/home-inspection-software/` (Next.js route) and **update `canonical` and `og:url`** to `https://vlx.ai/home-inspection-software/`.
2. **301** from `/vlx-home/` → `/home-inspection-software/` (and any already-linked variant) to keep equity and avoid leaving the old URL indexable.
3. Add a **visible breadcrumb** (Home › Home Inspection Software) + keep the `BreadcrumbList` schema.
4. The 9 specialty sub-pages nest under this route: `/home-inspection-software/roof/`, `/home-inspection-software/wind-mitigation/`, etc. (confirm the child-slug pattern; the CSV's provisional scheme relocates here).
5. Link the page **from the hub** `/digital-inspections-software/` to preserve topical linking.

Decide/implement **before launch** to avoid chaining 301s later.

### 5.3 Internal linking — OK (improvement `P2`)
84 links (mostly nav/footer). Links to KYPiT, COUNTiT, `/home-inspectors/`, overview and inspection-companies. Pricing mentions Spectora but **there is no `/vs-spectora` link** → add it (conquesting; Spectora ≈9,900/mo). The sub-service hub (§1.2) would add 9 keyword-rich internal links.

---

## 6. Launch checklist

1. [ ] PROD `index,follow` (`APP_ENV` gate) + 200 + sitemap.
2. [ ] **Primary keyword = `home inspection software`**; title/H1/meta/H2/alt aligned.
3. [ ] Defined keyword map reinstated (primary + secondaries + sub-service hub §1).
4. [ ] Primary density raised to 4-6 natural uses.
5. [ ] 9-specialty sub-service hub (inert "Page coming" links) recovered.
6. [ ] Hero/LCP: `priority` (eager + fetchpriority high), not lazy.
7. [ ] `SoftwareApplication.offers` with price; `aggregateRating` out.
8. [ ] `og:type` + `og:image` PROD verified.
9. [ ] `/home-inspectors/` re-focus confirmed (anti-cannibalization, Option A).
10. [ ] **URL = `/home-inspection-software/`** (not `/vlx-home/`) + 301 + canonical + og:url.
11. [ ] Visible breadcrumb added (Home › Digital Inspections Software › Home Inspection).
12. [ ] Validate all 5 schemas in Rich Results Test.
13. [ ] Measure CWV in PSI/CrUX; `/vs-spectora` link.

---

*Rev. 2 (2026-10-01): corrects the primary keyword to `home inspection software` and incorporates the defined keyword map (CSV `VLX-Home-subpaginas-keywords.csv` + brief). Volumes not re-verified live (Ranki out of credits at the time); based on prior research.*
