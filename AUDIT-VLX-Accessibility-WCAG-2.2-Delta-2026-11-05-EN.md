# Accessibility Re-Audit — WCAG 2.2 Delta
**Site:** vlx.ai (production)
**Date:** 2026-11-05
**Scope:** The **6 new A/AA success criteria** that WCAG 2.2 adds over WCAG 2.1. Everything already covered in the WCAG 2.1 AA audit (see `AUDIT-VLX-Accessibility-WCAG-2026-10-06-EN.md`) is intentionally **omitted** here.
**Why 2.2:** WCAG 2.2 is the current W3C Recommendation and the standard VLX's own Accessibility Statement commits to ("WCAG 2.2 Level AA, where reasonably achievable"). Because the prior pass covered all of 2.1 A+AA, the only net-new conformance checks are the 2.2 additions.
**Method:** live inspection with the built-in browser (computed geometry, fixed-overlay / scroll-padding analysis, keyboard focus, target-size measurement). New criteria confirmed against the official W3C source (w3.org/WAI — "What's New in WCAG 2.2").

---

## The 6 new criteria in WCAG 2.2 (A/AA)
| SC | Criterion | Level | Status |
|----|-----------|:-:|:-:|
| 2.4.11 | Focus Not Obscured (Minimum) | AA | ❌ Fail |
| 2.5.7 | Dragging Movements | AA | ✅ Pass |
| 2.5.8 | Target Size (Minimum) | AA | ❌ Fail |
| 3.2.6 | Consistent Help | A | ✅ Pass |
| 3.3.7 | Redundant Entry | A | ➖ N/A |
| 3.3.8 | Accessible Authentication (Minimum) | AA | ➖ N/A (out of scope) |

> *(WCAG 2.2 also **removes** 4.1.1 Parsing. The duplicate-ID item from the 2.1 report therefore no longer counts as a conformance failure under 2.2 — it remains a markup-hygiene recommendation only.)*

**Verdict:** 2 new failures, both low-effort fixes. No regressions in the other 2.2 additions.

---

## New findings

### ❌ 2.4.11 Focus Not Obscured (Minimum) — AA
When a component receives keyboard focus it must not be **entirely hidden** by author-created content (e.g. a sticky header).
- **Evidence:** the page has a stack of fixed elements at the top — announcement bar (`.vlx-announce-bar`, 40px) + sticky `<header>` (68px) = **108px** covered band. But `scroll-padding-top` on `<html>` is only **40px**.
- **Consequence:** when a keyboard user tabs to an element below the fold (or follows an in-page anchor), the browser scrolls it to 40px from the top — i.e. **~68px behind the sticky header** — so the focused element is partially/fully obscured.
- **Fix:** set `scroll-padding-top` to the full fixed stack height (≈108px), e.g. `html { scroll-padding-top: 108px; }` (ideally driven by the same `--vlx-announce-*` / header-height variables).
- **Impact:** Medium · **Effort:** Low · **Pages:** all (shared header).

### ❌ 2.5.8 Target Size (Minimum) — AA
Pointer targets must be at least **24×24 CSS px**, unless an exception applies (inline-in-text, or a 24px-diameter spacing circle that doesn't overlap neighbors).
- **Footer social icons** (`a[aria-label^="Follow VLX"]` — LinkedIn, YouTube, Instagram, Facebook, Capterra, G2): measured **~5px wide × 36px tall** (16×16 SVG, no padding), with only **~13px between centers**. Below 24px **and** the spacing exception fails (circles overlap). → **Fail.**
- **Cookie banner "Decline" button:** **43 × 16px** — height below 24px. ("Accept" is 73×32 and passes, so Decline is also the smaller/harder target of the pair.) → **Fail.**
- **Fix:** give these targets a minimum hit area of 24×24px (padding on the social `<a>`s; taller padding on "Decline"), and keep ≥24px spacing between the social icons.
- **Impact:** Medium (mobile/motor users) · **Effort:** Low · **Pages:** footer (all) + cookie banner (all).

---

## Passing / not applicable (new in 2.2)

### ✅ 2.5.7 Dragging Movements — AA
No drag-based functionality found (no `input[type=range]` sliders, no `draggable=true`, no drag carousel/swiper). The "One Platform" use-case section is a responsive grid, not a draggable carousel. All functionality is operable with simple clicks. *(This also resolves the earlier 2.5.1 "verify single-pointer alternative" manual item.)*

### ✅ 3.2.6 Consistent Help — A
Help mechanisms — Zoho chat widget (site-wide), Contact (`/about-us/contact/`), FAQs (`/faqs/`), Help Center (`help.vlx.ai`) — live in the shared header/footer and the globally injected chat, so they appear in a consistent relative order across pages.

### ➖ 3.3.7 Redundant Entry — A
No multi-step process on the marketing site that re-requests previously entered information (the contact form is single-step; the demo is a Google Calendar widget). Not applicable.

### ➖ 3.3.8 Accessible Authentication (Minimum) — AA
vlx.ai (marketing) has no authentication of its own; "Login" / "Start Free Trial" link out to **app.vlx.ai**. Out of scope for this audit. **Recommendation:** audit the app's sign-in separately to confirm it doesn't require a cognitive-function test (e.g. a CAPTCHA/puzzle) without an accessible alternative.

---

## Methodology
Checks run on the home page and confirmed against shared components (header, footer, cookie banner, chat) that repeat site-wide. New-criteria definitions taken from the official W3C WCAG 2.2 materials. This delta is additive to the WCAG 2.1 AA audit dated 2026-10-06; read both together for the full picture.
