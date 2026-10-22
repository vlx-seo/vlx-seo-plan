# SEO Considerations — Migration of `app.visualogyx.com` → `app.vlx.ai`

**Scope:** This document covers **only the SEO-relevant** considerations for moving the application subdomain from `app.visualogyx.com` to `app.vlx.ai`. Infrastructure, authentication, payments, and mobile-app concerns are intentionally out of scope here.

**Date:** 2026-10-21

---

## 0. First, assess indexability (do this before anything else)

The SEO impact of this migration depends heavily on **whether `app.visualogyx.com` is actually indexed by Google**.

- The VLX application runs behind authentication (Cognito), so most of it is likely `noindex` or unreachable to crawlers.
- **Action:** Confirm the real indexed footprint of `app.visualogyx.com` before planning effort:
  - `site:app.visualogyx.com` in Google.
  - Search Console → *Pages* / *Coverage* for the `app.` property (if one exists).
  - Check the subdomain's `robots.txt` and page-level `<meta robots>` / `X-Robots-Tag`.

**Why it matters:** If the subdomain has few or zero indexed URLs, the SEO work is mostly about **link-equity consolidation and brand consistency**, not about preserving rankings. If it has indexed pages (login landing, marketing-style app pages, public reports, shared links), then the full redirect + reindexing workflow below applies.

---

## 1. Redirects (the single most important SEO action)

- Implement **301 (permanent) redirects**, server-side, from `app.visualogyx.com/*` to `app.vlx.ai/*`.
- **Preserve the full path and query string** — every old URL must land on its exact equivalent, never a blanket redirect to the homepage. Blanket redirects to root are treated as soft-404s and lose all equity.
- **Keep `app.visualogyx.com` alive serving these redirects for a long time (minimum 1 year, ideally indefinitely).** Backlinks, old emails, and printed QR codes will keep hitting the old domain.
- **Avoid redirect chains/hops.** The old URL should reach the final new URL in a **single hop** (watch for `http → https → www → new-domain` stacking).
- **Do not block the old domain in `robots.txt`.** Google must be able to crawl the old URLs to *see* the 301s and transfer signals. Blocking it freezes the migration.

---

## 2. Search Console & Bing Webmaster Tools

- **Add and verify `app.vlx.ai`** as a new property in Google Search Console and Bing Webmaster Tools.
- **Keep the old `app.visualogyx.com` property** active to monitor de-indexing of old URLs and catch errors during the transition.
- **Change of Address tool:** Note this tool in GSC is designed for **root-domain** moves (`visualogyx.com → vlx.ai`). For a **subdomain** move it may not be available/applicable; in that case Google relies entirely on the 301s to transfer signals. Verify availability; if unavailable, the redirects do the work.
- **Submit the new sitemap** (see §3) in both tools.
- Monitor **Index Coverage / Pages** on both properties: old URLs should drop out as new URLs get indexed.

---

## 3. Sitemaps & robots.txt

- Generate/update the XML sitemap for `app.vlx.ai` with the **new canonical URLs** (only for publicly indexable pages — don't list auth-gated URLs).
- Submit the new sitemap in GSC/Bing.
- Ensure `app.vlx.ai/robots.txt` is correct and does **not** accidentally disallow pages that should be indexed.
- Leave the **old sitemap reachable** for a while so Google re-crawls the old URLs and processes the redirects.

---

## 4. Canonicals, structured data & on-page signals

- **Canonical tags:** Any indexable page must self-canonicalize to its **`app.vlx.ai`** URL. Make sure no page on the new domain carries a leftover canonical pointing back to `app.visualogyx.com`.
- **Structured data (Schema.org):** Update any `url`, `sameAs`, `logo`, or `WebSite`/`Organization` references that contain `app.visualogyx.com`.
- **Open Graph / Twitter Card** `og:url` tags that reference the old domain.
- **hreflang** (if the app serves localized/public pages): update the alternate URLs to the new domain.

---

## 5. Internal linking across the whole ecosystem

Every link to `app.visualogyx.com` from properties **you control** should be updated to `app.vlx.ai` directly (don't rely on the redirect for your own links — direct links are cleaner and faster for both users and crawlers):

- Marketing site `vlx.ai` (home, service pages, headers/footers, CTAs, "Login" / "Sign up" / "Launch app" buttons).
- **Blog posts** and checklists/use-case/report pages.
- Any hardcoded links in templates, components, or CMS content.
- **Action:** Crawl `vlx.ai` (and the blog) for outbound links containing `app.visualogyx.com` and replace them.

---

## 6. External links, citations & listings (off-page SEO)

These pass link equity and/or brand citations; update the high-value ones even though the 301 preserves most equity:

- **Google Business Profile (GMB)** — website/app link.
- **App Store & Play Store** listings — any web links in the description.
- **Directories & review sites:** G2, Capterra, Trustpilot, etc. (keep NAP/brand citations consistent).
- **Social profiles & bios:** LinkedIn, X/Twitter, Facebook, YouTube.
- **Backlink audit:** Pull the backlink profile for `app.visualogyx.com` (Ahrefs / Ranki). For the **highest-authority referring domains**, request they update the link to `app.vlx.ai`. Low-value links can rely on the 301.
  - *Note:* `vlx.ai` returns **403 to any user agent containing "AhrefsBot"** at the ALB. When auditing with Ahrefs, verify suspicious 403s with a Googlebot UA before treating them as broken pages — they're usually the bot block, not real errors.

---

## 7. Brand & domain authority consolidation

- Moving the app under the `vlx.ai` brand **consolidates brand signals** under a single domain family — a positive for long-term authority, provided redirects and citations are handled.
- Keep **brand-name consistency** everywhere (app, store listings, directories) so brand-search and entity signals reinforce `vlx.ai`.

---

## 8. Timing & rollout

- Execute the redirect cutover and link updates **as close together as possible** (ideally one coordinated push), not trickled out over weeks — partial states create redirect inconsistencies and crawl confusion.
- Prefer a **lower-traffic window** for the cutover.
- Have the new sitemap ready to submit **immediately** after cutover to speed re-crawling.

---

## 9. Post-migration monitoring (first 4–8 weeks, then taper)

Track on **both** domains:

- **404 / crawl errors** (GSC → Pages, *Not found*, *Redirect error*).
- **Index coverage:** old URLs dropping, new URLs appearing.
- **Organic traffic & impressions/clicks** (GA4 + GSC) — watch for drops beyond the normal migration dip.
- **Rankings** for any keywords the app URLs ranked for (Ranki / GSC queries).
- **Redirect health:** periodic check that every old URL still returns a single 301 to the correct new URL (redirects tend to silently break over time).
- Confirm the **old domain is not being blocked** and the redirects remain live.

---

## Priority summary

| Priority | Action |
|----------|--------|
| **P0** | Confirm indexability of old subdomain; implement path-preserving 301s; keep old domain alive serving them |
| **P0** | Don't block old domain in robots.txt; avoid redirect chains |
| **P1** | Add new GSC/Bing property; submit new sitemap; keep old property for monitoring |
| **P1** | Update internal links (vlx.ai, blog) + canonicals/structured data to new domain |
| **P2** | Update external citations (GMB, stores, directories, social); backlink outreach for top referrers |
| **P2** | Post-migration monitoring of 404s, index coverage, traffic, rankings |

---

*Note: If the audit in §0 shows the old subdomain has little or no indexed footprint, the practical SEO work collapses to: set up the 301s, keep the old domain alive, update internal links, and update external brand citations. The reindexing/ranking-preservation steps become largely moot.*
