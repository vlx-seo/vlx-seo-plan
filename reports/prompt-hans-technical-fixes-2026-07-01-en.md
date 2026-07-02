# PROMPT — VLX Pre-Launch URGENT Fixes (2026-07-01 follow-up) — for Hans/Abe + their Claude

> **How to use:** paste everything below into a Claude Code session open on the **VLX-Marketing** repo. These are the URGENT items from the 2026-07-01 review of **dev.vlx.ai** (post-migration audit follow-up). Do the **Pre-launch blockers** first — do NOT launch until they pass. The **Post-launch** list at the end can wait. Source: SEO follow-up 2026-07-01 (Laura / Software Craft CR), integrated in `reports/VLX-AUDIT-REVIEW-2026-06-19.md`.

---

You are a senior dev in the VLX-Marketing repo (Next.js 15 App Router, `src/app/(frontend)`, Payload CMS, Tailwind, Framer Motion). The site is **pre-launch on dev.vlx.ai** (behind AWS ALB + Cognito); production is still WordPress. Market: USA + Canada, English. Implement each fix, verify, and report. Ask before destructive changes.

## STEP 0 — Confirm the deployed branch
The homepage H1 on dev.vlx.ai now reads **"The AI-Native Enterprise Platform for [Inspections/…]"** (typewriter), different from the `Hans-review` branch H1 "Run Your Inspection Business on VLX". **Confirm which branch/environment is deployed to dev.vlx.ai** before assuming file paths — the review may have hit a different branch.

## Pre-launch blockers (must fix before launch)

1. **[CRITICAL] Paid landing pages still 404.** The 13 paid LPs from `AUDIT-MIGRATIONS-EN-2026-05-28` (Capterra / G2 / LinkedIn: `/landing-inspection-software-capterra-a/`, `/landing-inspection-software-g2/`, `/landing-inspections-software-linkedin/`, etc.) return **404** on the new site. `/lp/` is disallowed in robots.txt but has **no content**. Per campaign, either create a dedicated lander (`/lp/[campaign]/`) OR 301 the old URL to the best-matching page — do **NOT** 404 paid clicks (wasted ad spend). Wire redirects in `next.config.ts`.

2. **[PRE-LAUNCH] Blog main pagination sends contradictory signals.** `sitemap.ts` lists `/blog/page/2…10/` (clean paths), but those redirect to `/blog/?page=N`, which is `noindex,nofollow` + canonical → `/blog/`. Either (a) remove `/blog/page/N/` from the sitemap, or (b) serve real server-rendered content at `/blog/page/N/` (index,follow, self-canonical). Category pagination already works — mirror it. Verify each `/blog/page/N/` returns 200 index/follow, or is absent from the sitemap.

3. **[PRE-LAUNCH] Old WordPress slugs 404.** `/oil-and-gas/` and `/financial-institutions/` return 404. Add redirects in `next.config.ts` → `/digital-inspections-software/oil-gas/` and `/digital-inspections-software/financial-services/`.

4. **[PRE-LAUNCH · verify] Hub LCP.** Audit 2026-06-19 measured 7.9s; the 2026-07-01 check saw TTFB 82ms / load event 354ms (likely fine) but could **not** run PSI (Cognito wall). Once dev is reachable without Cognito (or on a preview URL), run mobile Lighthouse/PSI on `/digital-inspections-software/` and confirm **LCP < 2.5s**.

5. **[PRE-LAUNCH] Blog post VISUAL errors** (source: *GTM – VLX Marketing Site – Blog Posts Review*). Fix **rendering/assets**, not HTML structure:
   - **Broken images (12 pages):** e.g. construction-industry-trends, hotel-room-housekeeping, why-audits-failed, difference-qms-eqms. Fix asset paths / hosting so the images load.
   - **Embedded / dynamic content not loading (6):** best-gemba-walk, sb-326-balcony-laws, hotel-maintenance, why-container-inspections, food-safety-checklist, and the "Sample Incident Report Template" on how-to-write-an-incident-report.
   - **Broken CTAs (2):** what-to-look-for-when-renting-a-houses (CTAs load no links/styles); photo-authentication-software (end-of-content CTA doesn't work).
   - **H1 shows a literal `&amp;` (7):** on-site-inspection-methods, inspection-software-that-prevents-fraud, financial-software-that-cuts-risk, 7-modern-inspection-methods, truck-inspection-checklist, how-to-write-an-incident-report, why-supply-chain-management-software-essential. Fix the double-encoding of `&` → `&amp;` in the post title / metadata render.
   - **Table missing data (1):** forklift checklist — "Sample Forklift Daily Inspection Form" doesn't load all rows; its lists also have an empty first item.

## When done (pre-launch gate)
1. `npm run build` → 0 errors.
2. `curl -I` the 13 paid LP URLs + `/oil-and-gas/` + `/financial-institutions/` → 301/200 (no 404).
3. Every `/blog/page/N/` in the sitemap → 200 index/follow, or removed from the sitemap.
4. Spot-check the visual-error pages render correctly (images, embeds, CTAs, H1 text, table).
5. Report what's done vs. what needs a decision. **Do NOT launch until 1–3 and 5 pass.**

## Post-launch (can wait — do NOT block launch)
- Icons not loading (6s-lean-audit, brc-audit); FAQ/list styles not loading (~14 pages); bad spacing (~8 pages); other render defects (text alignment, content inside a `<!Doctype html>` on virtual-inspections-shaping-the-future, visible `<code>` tags on seeing-risk-before-it-spreads).
- Titles/meta out of range (39 titles empty/>60c, 47 meta empty/>160c); 46/185 pages missing `og:image` (prioritize the 12 industry pages); decorative hub icons missing `alt`.
- **Note:** list-structure HTML fixes depend on the site builder (pending) — out of scope here.

> **SEO note (Laura):** 308 = 301 for Google — don't spend time swapping those. Prioritize the paid LPs, the blog-pagination signal, and the visual breakage. Full context: `reports/REVIEW-DEV-VLX-AI-2026-07-01.md` + the SEGUIMIENTO 2026-07-01 section of `reports/VLX-AUDIT-REVIEW-2026-06-19.md`.
