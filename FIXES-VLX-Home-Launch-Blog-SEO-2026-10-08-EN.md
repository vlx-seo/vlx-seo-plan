# SEO Fixes — "VLX Introduces VLX Home" Launch Blog Post

**URL:** https://vlx.ai/blog/vlx-introduces-vlx-home-an-ai-native-platform-for-home-inspection-businesses/
**Audit date:** 2026-10-08
**Author:** lceballos-seo
**Primary keyword:** `home inspection software` (modifier: `AI home inspection software`)

> **Volume note:** The Ranki API wallet was at **0 credits** at audit time, so priorities below are based on search intent, not exact volume/KD. Re-validate figures (`home inspection software`, `AI home inspection software`, `home inspection business software`, `home inspection scheduling/report software`) once the wallet is topped up.

> **Cannibalization guardrail:** `/home-inspectors/` (service page) and the VLX Home landing (`/digital-inspections-software/home/`) already target the commercial head term. This blog post should own the **news + AI angle** (`AI home inspection software`) and **link** to the commercial page using `home inspection software` as anchor text — it should not compete against our own landing for the head term.

> **Related doc:** This plan covers the **launch blog post** only. The VLX Home **landing page** on-page/technical audit lives in `AUDIT-VLX-Home-SEO-2026-10-01.md` (rev.4) in this repo — keep both consistent.

---

## 1. Primary-keyword placement checklist

| Element | Status | Current value | Fix |
|---|---|---|---|
| Meta title | OK | `VLX Home Early Access \| AI Home Inspection Software` | Valid. Optional: move keyword ahead of the brand. |
| Meta description | PARTIAL | "...an AI-native home inspection **platform**..." | Says "platform", not "software". Rewrite (§3). |
| H1 | PARTIAL | "...AI-Native **Platform** for Home Inspection Businesses" | Does not contain "software". Rewrite (§3). |
| H2 | MISSING | No H2 contains the keyword | Rename one H2 (§3). |
| Image ALT | MISSING | Featured image ALT = `VLX Home` only | Rewrite ALT with keyword (§3). |
| First 10% of content | PARTIAL | "Digital Inspection Software" / "home inspection solution" present; exact phrase absent | Adjust the dek/subtitle (§3). |

---

## 2. Additional findings

### Critical
1. **Two `<h1>` tags on the page.** The rendered HTML contains 2 H1 elements; there must be exactly one. This is template-level (likely visual title + article title), so it probably affects every blog post — fix in the blog template, not just this URL.

### Important
2. **Brand-first framing.** Meta title, H1 and meta description all lead with "VLX Home" — a brand term with **zero search volume**. The commercial keyword is relegated to the end or missing.
3. **Plain-text URL used as CTA.** "can visit vlx.ai/vlx-home/" is rendered as plain text, not a link (`<a>`). The URL appears 4× without a hyperlink. Convert to a descriptive anchor.
4. **Weak internal linking.** No contextual, keyword-anchored internal links to the commercial landing or to `/home-inspectors/`. (Recurring pattern across VLX blog clusters.)
5. **Cannibalization risk.** See guardrail above — define blog vs. landing roles so the post does not compete with our own commercial pages for `home inspection software`.

### Minor / enhancement
6. **No H3 structure.** The page has zero H3s. Sub-features (AI Walkthrough, AI Scribe, AI Template Builder) should be H3 for better semantic structure and long-tail capture.
7. **Weak featured-image ALT** (`VLX Home`). Many images use empty `alt=""` (decorative — fine), but the featured and in-content images should describe the scene + keyword.
8. **Meta description has no CTA** and uses different wording ("platform") than the title ("software"); align them.

### Correct — do not change
- `rel="canonical"` self-referential — OK
- `robots: index, follow` — OK
- JSON-LD schema: `BlogPosting` + `BreadcrumbList` + `Organization` — OK
- hreflang configured: en-US / en-CA / en-CO / en-MX / x-default — OK
- Open Graph + Twitter `summary_large_image` with `og:image` — OK

---

## 3. Proposed rewrites (ready to paste)

> The recommended variants keep the announcement framing **and** insert the keyword, while avoiding cannibalization: the blog keeps `AI home inspection software` and links the landing with the `home inspection software` anchor.

**Meta title** (~55 chars) — optional, current is already valid:
```
AI Home Inspection Software | VLX Home Early Access
```

**Meta description** (~150 chars):
```
VLX Home is AI-native home inspection software that unifies booking, scheduling, mobile inspections, and AI reporting in one platform. Request Early Access.
```

**H1** (single):
```
VLX Introduces VLX Home: AI-Native Home Inspection Software for Inspection Businesses
```

**Subtitle / dek (first 10%):**
```
VLX expands beyond the field with AI-native home inspection software that brings the entire home inspection workflow together.
```

**H2 to rename** (the third one):
- From: `A purpose-built vertical for home inspections`
- To: `Purpose-built home inspection software for inspection businesses`

**Featured image ALT:**
```
AI-native home inspection software VLX Home shown on a tablet during a property inspection
```

**Early Access CTA** (convert URL → anchor):
- From: `...can visit vlx.ai/vlx-home/.`
- To: `...can [request Early Access to VLX Home](/vlx-home/).`

**H3 to add** (inside "AI built into the inspection process"):
`AI Walkthrough`, `AI Scribe`, `AI Template Builder` as `<h3>`.

**Internal links to add** (anchor → destination):
- `home inspection software` → VLX Home commercial landing (`/digital-inspections-software/home/`).
- `digital inspection software` → VLX Digital Inspection Software page.
- `home inspectors` → `/home-inspectors/`.

---

## 4. Suggested execution order

1. Fix the **duplicate H1** (blog template) — verify it does not break other posts.
2. Apply the **H1 / meta title / meta description / ALT / subtitle / H2** rewrites (§3).
3. Convert the **plain URL → anchor** and add **keyword-anchored internal links**.
4. Add **H3s** to the sub-features.
5. Confirm the **blog vs. landing role** with Laura to close the cannibalization question.
6. Once Ranki has credits: validate volume/KD for the primary and secondary keywords and re-prioritize.

---

## 5. Secondary keywords to work into the body (feature already described, just not named)

- `home inspection business software` / `software for home inspection business`
- `home inspection scheduling software` / `online booking for home inspectors`
- `home inspection report software` / `AI inspection report writer`
- `home inspection app` (mobile inspections)
- `AI home inspection report`
