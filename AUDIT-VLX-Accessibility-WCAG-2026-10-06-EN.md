# Accessibility Audit — WCAG 2.1 AA
**Site:** vlx.ai (production)
**Date:** 2026-10-06
**Scope:** 8 pages / 7 templates · Target level: AA (50 criteria, A + AA)
**Reference standard:** WCAG 2.1 AA — the technical standard referenced by the DOJ 2024 rule (ADA Title II), Section 508, and EN 301 549 Ch. 9. *(VLX's public statement claims it aims for WCAG 2.2 AA "where reasonably achievable.")*
**Method:** live inspection with the built-in browser (accessibility tree, computed color contrast, keyboard and focus testing, reflow at 320px). Tooling: `wcag-audit` skill series (orchestrator + 4 per-principle skills).

---

## Verdict
**❌ NOT AA-CONFORMANT**
> A site is AA-conformant only if all 50 applicable criteria pass. There are **4 confirmed failures** and **12 criteria requiring manual review** (several with a high probability of failing). The site's technical foundation is **solid** (semantic structure, keyboard, focus, reflow); the dominant problem is **color contrast**.

| Level | ✅ Pass | ❌ Fail | ⚠️ Manual review | ➖ N/A |
|-------|:-:|:-:|:-:|:-:|
| A (30)  | 14 | 2 | 7 | 7 |
| AA (20) | 10 | 2 | 5 | 3 |
| **Total (50)** | **24** | **4** | **12** | **10** |

---

## Priority fixes
Ordered by impact × effort. Start here.

| # | Problem | Criterion | Impact | Effort | Pages affected |
|---|---------|-----------|--------|--------|----------------|
| 1 | **Brand-orange (#FF5722) contrast too low.** White text on orange buttons and orange text/links on light backgrounds measure **~3.0–3.2:1** (4.5:1 required). Affects CTAs ("Start Free Trial", "Get Started"), nav menu links ("Solutions"), "View All Posts", in-article links. | 1.4.3 | **High** | Medium | All (shared header/footer + body) |
| 2 | **Light-gray text is unreadable.** Descriptions and stat labels in gray-300/gray-400 measure **1.5–2.5:1**. | 1.4.3 | **High** | Low | Home, service, blog |
| 3 | **Contact form missing `autocomplete`.** Name/email/phone fields don't declare their purpose; required fields ("*") also don't expose `required`/`aria-required` (visual asterisk only). | 1.3.5, 3.3.2 | Medium | Low | /about-us/contact/ |
| 4 | **Duplicate ID site-wide** (`zsiqscript` ×3, injected by the Zoho SalesIQ widget). | 4.1.1 | Low | Low | All |
| 5 | **Ambiguous links** ("read more", "here", "this") without programmatic context. | 2.4.4 | Medium | Low | Home, blog |
| 6 | **Status messages not announced.** No `aria-live`/`role=status` region exists anywhere on the site; the contact form result and any dynamic updates are likely not communicated to screen readers. | 4.1.3 | Medium | Low | Forms / dynamic UI |

---

## What's done WELL (solid foundation)
- **Correct semantic structure:** one `<h1>` per page, no heading-level skips, landmarks (`header/nav/main/footer`), real lists and tables. (1.3.1, 2.4.6)
- **"Skip to main content" link** present on every template. (2.4.1)
- **Descriptive, unique page titles**, including the 404. (2.4.2)
- **Keyboard:** native controls; dropdown menus as `<button>` with `aria-expanded`; FAQ accordion toggles state correctly. (2.1.1, 4.1.2)
- **Visible focus:** uses `:focus-visible` (8 rules), no global `outline:none`; visible focus ring verified. (2.4.7)
- **Perfect reflow at 320px** (no horizontal scroll) and no orientation lock. (1.4.10, 1.3.4)
- **`lang="en"`** correct; honors **`prefers-reduced-motion`**. (3.1.1)
- **Images carry `alt`** (none missing the attribute); nameless SVGs are decorative and accompany text. (1.1.1)
- **Demo iframe has a `title`** ("Schedule a Demo with VLX"). (4.1.2)

---

## Findings by criterion

### Principle 1 — Perceivable
| SC | Criterion | Level | Status | Finding & location |
|----|-----------|:-:|:-:|------|
| 1.1.1 | Non-text Content | A | ⚠️ | Images have `alt` (OK). 11 standalone decorative SVGs without `aria-hidden="true"` (recommendation, not a failure). Manually confirm none convey unique information. |
| 1.2.1–1.2.5 | Time-based Media (audio/video) | A/AA | ➖ | No video or audio found in the sample. Confirm there are no product videos on non-sampled pages. |
| 1.3.1 | Info and Relationships | A | ✅ | Headings, landmarks, lists and labels present in the markup. |
| 1.3.2 | Meaningful Sequence | A | ✅ | DOM order matches visual order. |
| 1.3.3 | Sensory Characteristics | A | ✅ | No sensory-only instructions detected. |
| 1.3.4 | Orientation | AA | ✅ | No orientation lock. |
| 1.3.5 | Identify Input Purpose | AA | ❌ | Contact form: name/email/phone fields have **no `autocomplete`**. |
| 1.4.1 | Use of Color | A | ⚠️ | FAIL/PASS states shown in red/green in product mockups and category tags; confirm they don't rely on color alone. Orange links: verify they carry an underline or other non-color cue. |
| 1.4.2 | Audio Control | A | ➖ | No auto-playing audio. |
| 1.4.3 | Contrast (Minimum) | AA | ❌ | **Systemic.** Brand orange on light ~3.0–3.2:1 and light gray 1.5–2.5:1 (4.5:1 / 3:1 large required). See fixes #1 and #2. |
| 1.4.4 | Resize Text | AA | ✅ | Text scales to 200% without loss. |
| 1.4.5 | Images of Text | AA | ✅ | Text is real text; only logos are images. |
| 1.4.10 | Reflow | AA | ✅ | 320px with no horizontal scroll. |
| 1.4.11 | Non-text Contrast | AA | ⚠️ | Orange buttons vs page 3.16:1 (≥3 OK as a component). Manually review input borders and informative icons for low contrast. |
| 1.4.12 | Text Spacing | AA | ⚠️ | Likely OK; confirm by applying the criterion's spacing overrides. |
| 1.4.13 | Content on Hover or Focus | AA | ⚠️ | Hover dropdown menus: verify they are dismissible (Esc), persistent, and don't vanish when moving the pointer toward them. |

### Principle 2 — Operable
| SC | Criterion | Level | Status | Finding & location |
|----|-----------|:-:|:-:|------|
| 2.1.1 | Keyboard | A | ✅ | Native controls; menus and accordion operable by keyboard. |
| 2.1.2 | No Keyboard Trap | A | ✅ | No traps in what was tested. Confirm in non-sampled modals/overlays. |
| 2.1.4 | Character Key Shortcuts | A | ➖ | No single-character shortcuts. |
| 2.2.1 | Timing Adjustable | A | ➖ | No time limits. |
| 2.2.2 | Pause, Stop, Hide | A | ⚠️ | Infinite decorative animations (shimmer, blinking cursor, pulse/ping). `prefers-reduced-motion` honored mitigates; no pause control. Low impact. |
| 2.3.1 | Three Flashes | A | ✅ | Nothing flashes more than 3×/sec. |
| 2.4.1 | Bypass Blocks | A | ✅ | Skip link present. |
| 2.4.2 | Page Titled | A | ✅ | Unique, descriptive titles. |
| 2.4.3 | Focus Order | A | ✅ | No positive `tabindex`; logical order. |
| 2.4.4 | Link Purpose (In Context) | A | ❌ | "read more"/"here"/"this" links without context (home, blog). |
| 2.4.5 | Multiple Ways | AA | ✅ | Menu + footer + blog/indexes as alternative routes. |
| 2.4.6 | Headings and Labels | AA | ✅ | Descriptive. |
| 2.4.7 | Focus Visible | AA | ✅ | `:focus-visible` used; visible ring verified. |
| 2.5.1 | Pointer Gestures | A | ⚠️ | Verify the use-case carousel has a single-pointer alternative (buttons), not swipe only. |
| 2.5.2 | Pointer Cancellation | A | ✅ | Standard controls (fire on pointer-up). |
| 2.5.3 | Label in Name | A | ✅ | Accessible name matches visible text. |
| 2.5.4 | Motion Actuation | A | ➖ | No device-motion features. |

### Principle 3 — Understandable
| SC | Criterion | Level | Status | Finding & location |
|----|-----------|:-:|:-:|------|
| 3.1.1 | Language of Page | A | ✅ | `lang="en"`. |
| 3.1.2 | Language of Parts | AA | ✅ | Monolingual content; no unmarked foreign-language passages. |
| 3.2.1 | On Focus | A | ✅ | Focusing does not trigger context changes. |
| 3.2.2 | On Input | A | ⚠️ | Confirm the form's "Subject" `select` doesn't navigate/submit on change alone. |
| 3.2.3 | Consistent Navigation | AA | ✅ | Header/footer consistent across pages. |
| 3.2.4 | Consistent Identification | AA | ✅ | Icons/labels consistent for the same function. |
| 3.3.1 | Error Identification | A | ⚠️ | Not tested (form not submitted in production). Verify in staging that errors are identified in text and point to the field. |
| 3.3.2 | Labels or Instructions | A | ⚠️ | Labels present ✅, but required fields ("*") **don't expose `required`/`aria-required`**; missing programmatic required marker. |
| 3.3.3 | Error Suggestion | AA | ⚠️ | Not tested in production. Verify in staging that a correction is suggested. |
| 3.3.4 | Error Prevention (Legal, Financial, Data) | AA | ➖ | No legal/financial transactions on the marketing site (the demo runs through Google Calendar). |

### Principle 4 — Robust
| SC | Criterion | Level | Status | Finding & location |
|----|-----------|:-:|:-:|------|
| 4.1.1 | Parsing | A | ❌ | Duplicate ID `zsiqscript` ×3 (Zoho SalesIQ), site-wide. *(Note: this criterion was removed in WCAG 2.2; prioritize as markup hygiene, not 2.2 conformance.)* |
| 4.1.2 | Name, Role, Value | A | ⚠️ | Mostly ✅ (nav with `aria-expanded`, accordion toggles state, iframe with `title`). Pending: expose the `required` state of form fields. |
| 4.1.3 | Status Messages | AA | ⚠️ | **No `aria-live`/`role=status` region exists anywhere on the site.** Verify the contact form's success/error message and any dynamic updates are announced to screen readers (likely failing). |

---

## Appendix — Requires manual review
What a person must verify (content/flows automated auditing can't judge):
- [ ] **3.3.1 / 3.3.3** — Submit the contact form **in staging** with invalid data and confirm errors are identified in text, point to the field, and suggest a correction. *(Not tested in production to avoid sending a real message.)*
- [ ] **4.1.3** — Confirm whether the form's confirmation/error message is announced (no live region in the markup).
- [ ] **1.4.1** — Confirm orange links carry an underline or other non-color cue, and that PASS/FAIL states don't rely on red/green alone.
- [ ] **1.4.13** — Test hover dropdown menus: dismissible with Esc, persistent, don't vanish when moving the pointer.
- [ ] **1.4.11 / 1.4.12** — Spot-check input borders and icons; apply text-spacing overrides.
- [ ] **2.5.1** — Verify a single-pointer alternative (buttons) for the use-case carousel.
- [ ] **1.2.x** — Confirm there are no product videos on non-sampled pages; if there are, they require captions/transcripts.
- [ ] **Demo (Google Calendar iframe)** — The widget's internal accessibility is Google's responsibility; offer a clearly accessible alternative (email/phone).

## Methodology
**Sample by template:** Home (`/`), Service (`/digital-inspections-software/quality/`), Own form (`/about-us/contact/`), Demo with iframe (`/demo/`), Blog post (`/blog/difference-qms-eqms/`), FAQs with accordion (`/faqs/`), 404, and the Accessibility Statement (`/accessibility/`).
**Not sampled** (the systemic contrast failures and the duplicate ID repeat because they come from the shared header/footer/script): remaining service pages, case studies, pricing, legal pages.
**Scope note:** form-error tests (3.3.1/3.3.3) were not run in production to avoid sending real data; they are left for staging.
