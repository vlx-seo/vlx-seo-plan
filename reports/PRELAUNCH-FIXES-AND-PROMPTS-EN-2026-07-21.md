# VLX Pre-Launch — Detailed Fixes & Correction Prompts

**Date:** 2026-07-21
**Repo / branch:** `Visualogyx/VLX-Marketing` — `master`
**Stack / deploy:** Next.js 15 + Payload CMS, AWS ECS + CodeBuild (GitHub Actions). Indexing gated by `APP_ENV`.
**Source audit:** `reports/AUDIT-PRELAUNCH-MASTER-ES-2026-07-21.md`
**Purpose:** Hand-off spec for dev (Abe / Claude Code). Each item has: what & where (with `file:line` evidence), why it matters, the fix, and a copy-paste correction prompt.

> **How to use the prompts:** paste each fenced prompt into Claude Code with the repo open. Line numbers are indicative — always locate the code by the quoted snippet, since numbers drift. Every prompt ends with a verification step.

---

## Guiding principle — every indexable URL must point to `vlx.ai`

**All indexable URLs the site emits must use the final production domain `https://vlx.ai` — never `prod.vlx.ai`, `dev.vlx.ai`, or any preview/Vercel host.** This is a standing acceptance criterion for every fix below that touches a URL, and for the migration itself. It applies to:

- **Canonicals** — `<link rel="canonical">` / `alternates.canonical`
- **Sitemap** — every `<loc>` in `sitemap.xml`
- **hreflang** — `alternates.languages` / `hreflang` annotations (if/when added; site is currently `en` only)
- **Open Graph & Twitter** — `og:url`, `og:image`, `twitter:image` (and any absolute social URL)
- **Image asset paths** — any image URL referenced in indexable markup (`next/image` `src`, `og:image`, schema `image`)

Non-canonical hosts (`prod.vlx.ai`, `dev.vlx.ai`) must stay behind Cognito so they are never crawled (see item 1). If any indexable URL resolves to a non-`vlx.ai` host, treat it as a launch blocker.

---

## 1. Migration / prod configuration (domain cutover)

### What & where
The rollout plan is: (1) point `vlx.ai` DNS to the tested ECS server (also reachable at `prod.vlx.ai`), and (2) remove the Cognito login gate. Today:
- Indexing switch: `src/lib/metadata.ts:15` → `robotsForEnv()` returns `index, follow` only when `APP_ENV === "prod"`.
- Prod task: `.aws/task-definition-prod.json:31-32` → `APP_ENV=prod`, `NEXT_PUBLIC_SITE_URL=https://prod.vlx.ai`.
- Canonicals: hard-coded to `https://vlx.ai` via `BRAND.url` (`src/lib/constants.ts:5`) — **domain-final already**.
- Sitemap: `BASE_URL = "https://vlx.ai"` hard-coded (`src/app/sitemap.ts:10`) — **domain-final already**.
- robots.txt sitemap ref: `https://vlx.ai/sitemap.xml` (`src/app/robots.ts:85`) — **domain-final already**.
- OG/`og:url`/`og:image`: resolved against `metadataBase = new URL(process.env.NEXT_PUBLIC_SITE_URL || "https://vlx.ai")` (`src/app/(frontend)/layout.tsx:30`) → **currently baked to `prod.vlx.ai`** (mismatch vs canonical).

### Why it matters
If Cognito is removed globally while `prod.vlx.ai` still resolves to the same server, `prod.vlx.ai` becomes a **public, crawlable, `index,follow` mirror** of `vlx.ai`. Canonicals point to `vlx.ai` and mitigate duplicate *indexing*, but the mirror is still crawlable (wasted crawl budget, indexing window, mixed `og:url` signal).

### Fix — recommended approach: **keep Cognito, do NOT mass-redirect**
1. **Keep the Cognito gate on the `prod.vlx.ai` host; expose only `vlx.ai` publicly.** On the ALB, add a host-based listener rule: `Host == vlx.ai` → forward to the target group **without** the `authenticate-cognito` action; keep the default rule (all other hosts, incl. `prod.vlx.ai` / `dev.vlx.ai`) behind Cognito. This blocks crawling of the non-canonical hosts at the edge — no 297-redirect host layer needed, no risk of redirect loops.
2. **Align OG to the final domain:** set `NEXT_PUBLIC_SITE_URL=https://vlx.ai` in `.aws/task-definition-prod.json` and **rebuild** (metadataBase is baked at build time). After this, `og:url`/`og:image` match the canonical (`vlx.ai`), regardless of which host serves.
3. **Verify (already domain-final, but confirm post-build):** canonicals, sitemap, robots.txt all emit `https://vlx.ai`.
4. **Post-launch:** in GSC confirm only `vlx.ai` is indexed; confirm `prod.vlx.ai` / `dev.vlx.ai` return the Cognito 302 to crawlers.

### Correction prompt
```
Context: Next.js 15 + Payload, deployed to AWS ECS. We are cutting over vlx.ai DNS
to this server at go-live. The non-canonical hosts (prod.vlx.ai, dev.vlx.ai) must
NOT become public/indexable mirrors — we will keep them behind Cognito rather than
301-redirecting them.

Do the following and report a diff:

1. In .aws/task-definition-prod.json, change the NEXT_PUBLIC_SITE_URL env var value
   from "https://prod.vlx.ai" to "https://vlx.ai". Leave APP_ENV=prod. This makes
   metadataBase (src/app/(frontend)/layout.tsx) produce og:url / og:image on the
   vlx.ai domain, matching the canonical tags.

2. Confirm (read-only, report findings, do NOT change) that these already emit the
   final domain https://vlx.ai and are NOT derived from NEXT_PUBLIC_SITE_URL:
   - Canonicals: src/lib/constants.ts (BRAND.url) and usages in metadata.ts.
   - Sitemap: src/app/sitemap.ts (BASE_URL).
   - robots.txt sitemap reference: src/app/robots.ts.

3. Print a short go-live verification checklist using curl as Googlebot against the
   built site, checking: <link rel="canonical"> = https://vlx.ai/..., og:url =
   https://vlx.ai/..., robots meta = "index, follow", and sitemap.xml URLs on vlx.ai.

Do NOT add any host-wide 301 redirect for prod.vlx.ai. The ALB Cognito rule (infra,
outside this repo) will gate the non-canonical hosts — note that as a manual infra
step in your summary.
```

**Infra note (outside repo, for whoever manages the ALB):** add an ALB listener rule `Host == vlx.ai → forward (no auth)`; keep default rule → `authenticate-cognito`. Do the DNS change and the auth-rule change together so `vlx.ai` is public exactly when the new site should go live, and `prod.vlx.ai` stays gated.

---

## 2. Canonical slug is `oil-gas` instead of `oil-and-gas`

### What & where
- Route lives at `src/app/(frontend)/digital-inspections-software/oil-gas/` (directory name `oil-gas`).
- `next.config.ts` redirects the keyword-correct slug **to** the weaker one:
  - `:204-205` `/oil-and-gas` → `/digital-inspections-software/oil-gas/`
  - `:215-216` `/digital-inspections-software/oil-and-gas` → `/digital-inspections-software/oil-gas/`
  - `:958-959` `/oil-and-gas-inspection-software` → `/digital-inspections-software/oil-gas/`
- The H1 already uses the right phrase: `OilGasContent.tsx:19` → "Oil and Gas Inspection Software for Field Operations".

### Why it matters
The target keyword is "oil **and** gas inspection software". The canonical URL should contain the natural query token `oil-and-gas`. Right now the canonical URL is the weaker `oil-gas`, and the SEO-preferred slug is 301'd away from — the opposite of the keyword strategy (`KW-URL-MAPPING`). Low-severity but it's a permanent URL decision, cheaper to fix pre-launch than after indexing.

### Fix
Make `oil-and-gas` the canonical slug and reverse the redirects:
1. Rename dir `digital-inspections-software/oil-gas/` → `digital-inspections-software/oil-and-gas/`.
2. Update every internal reference to the slug (hub industries array, navbar `INDUSTRIES`, sitemap, canonical/metadata, any `<Link>`).
3. In `next.config.ts`: remove/flip the `oil-and-gas → oil-gas` redirects; add `**/oil-gas/** → **/oil-and-gas/**` (301) so any old `oil-gas` links and prior indexing are preserved.
4. Verify no broken internal links and that the page still builds.

### Correction prompt
```
Goal: change the canonical URL slug of the Oil & Gas industry page from
"oil-gas" to "oil-and-gas" (the target keyword is "oil and gas"), and preserve
all old URLs via 301s.

Steps:
1. Rename the route directory:
   src/app/(frontend)/digital-inspections-software/oil-gas/
   -> src/app/(frontend)/digital-inspections-software/oil-and-gas/

2. Grep the whole repo (excluding node_modules and next.config.ts) for the string
   "oil-gas" and update EVERY internal reference to "oil-and-gas". Expected hits:
   the hub industries array (src/app/(frontend)/digital-inspections-software/HubContent.tsx),
   the navbar INDUSTRIES constant (search src/ for INDUSTRIES), the sitemap
   (src/app/sitemap.ts), and any canonical/metadata/href inside the OilGas page/content.
   Do NOT change occurrences inside blog-post slugs (e.g. the existing
   "...steel-pipes-in-the-oil-gas-industry" blog redirect) — those are unrelated
   editorial URLs; only change the industry route slug.

3. In next.config.ts, fix the redirects:
   - Change the destinations that currently point to
     "/digital-inspections-software/oil-gas/" so they point to
     "/digital-inspections-software/oil-and-gas/" (the sources /oil-and-gas,
     /digital-inspections-software/oil-and-gas, /oil-and-gas-inspection-software).
   - ADD a new permanent redirect: source
     "/digital-inspections-software/oil-gas" -> destination
     "/digital-inspections-software/oil-and-gas/" so the old slug 301s to the new one.
   - Ensure there is no redirect loop (old slug -> new slug only, never the reverse).

4. Run `npm run build` (or at least type-check the changed files) and grep again for
   "oil-gas" to confirm no stale internal link remains. Report the diff and any
   remaining references.
```

---

## 3. Blog schema lists only 12 posts + count mismatch

### What & where
- `src/app/(frontend)/blog/page.tsx:108-109`:
  - `:108` `numberOfItems: allPosts.length,` (real total, ~100+ posts)
  - `:109` `itemListElement: allPosts.slice(0, 12).map(...)` (only 12 enumerated)

### Why it matters
`ItemList.numberOfItems` declares the full count, but only 12 `ListItem`s are emitted. Google flags the mismatch between the declared count and the actual list length; the `CollectionPage`/`ItemList` markup for the blog index is therefore inconsistent (and under-represents the blog). Not a crawlability issue (the HTML lists posts fine) — a structured-data correctness issue.

### Fix
Enumerate all posts so `itemListElement` matches `numberOfItems` (drop the `.slice(0, 12)`). A large JSON-LD ItemList is valid. If a cap is genuinely desired, cap `numberOfItems` to the same sliced length instead — but matching the real total is preferred for the blog index.

### Correction prompt
```
File: src/app/(frontend)/blog/page.tsx

The CollectionPage JSON-LD builds an ItemList where numberOfItems = allPosts.length
but itemListElement only maps allPosts.slice(0, 12), so the declared count and the
number of ListItems disagree.

Fix: remove the ".slice(0, 12)" so itemListElement maps the full allPosts array,
keeping the "position" index sequential (1-based) across all items. Leave
numberOfItems = allPosts.length. Do not change the visible HTML rendering — only the
JSON-LD mainEntity.itemListElement.

Verify: after the change, numberOfItems equals itemListElement.length. Paste the
resulting JSON-LD snippet for the blog index in your summary.
```

---

## 4. Structured data — semantic gaps

Several JSON-LD issues (all semantic, no malformed syntax). Grouped here; sub-prompts can be run independently.

### 4a. Hub uses `CollectionPage.hasPart` (WebPage) instead of `ItemList`
- **Where:** `src/app/(frontend)/digital-inspections-software/page.tsx:34-48` — `CollectionPage` with a `hasPart` array of `WebPage` nodes for the 18 child pages; no `ItemList`/`ListItem`.
- **Why:** the hub is the canonical "collection of solutions" page; an `ItemList` with ordered `ListItem`s (each pointing to a child URL) is the expected pattern for a hub and is eligible for richer treatment than a bare `hasPart`.
- **Fix:** add an `ItemList` (as `mainEntity` of the `CollectionPage`) with one `ListItem` per child (position + `url`), reusing the existing `HUB_CHILDREN` array.

### 4b. Blog posts typed as `Article` instead of `BlogPosting`
- **Where:** `src/app/(frontend)/blog/[slug]/page.tsx:183` → `"@type": "Article"`. `BreadcrumbList` is already present (`:166-169`).
- **Why:** `BlogPosting` is the precise subtype for blog content and the type the audit strategy expects; `Article` is valid but less specific.
- **Fix:** change `@type` from `"Article"` to `"BlogPosting"` (all other fields stay). Apply the same to `case-studies/[slug]` only if desired (Article is fine there).

### 4c. Duplicate `SoftwareApplication` without `@id` (breaks entity linking)
- **Where:** `integrations/[slug]/page.tsx:62-75` and `kypit/page.tsx:15` emit a second `SoftwareApplication`/`Organization` **without `@id`**, so they don't link to the canonical nodes (`#software` in `src/lib/schema.ts:70-75`, `#organization`). `countit/page.tsx:16` does it correctly (references `https://vlx.ai/#organization`).
- **Why:** unlinked duplicate entities fragment the knowledge graph; `SuperTemplate.tsx:757-763` explicitly warns against this collision pattern.
- **Fix:** give these nodes the canonical `@id`s (`https://vlx.ai/#software`, `https://vlx.ai/#organization`) or reference them via `@id` instead of re-declaring, matching the `countit` pattern.

### 4d. `AggregateOffer` prices ignore the annual plan
- **Where:** `src/lib/schema.ts:81-88` → `lowPrice: "35"`, `highPrice: "55"`; but `PRICING` (`src/lib/constants.ts:48-49`) has annual 29 / 47. Real `lowPrice` available is 29, not 35.
- **Why:** understating availability (`lowPrice`) is a potentially misleading price signal.
- **Fix:** set `lowPrice` to the true lowest (annual, "29") and keep `highPrice` as the true highest; or expose both billing periods. Keep `priceCurrency: "USD"`.

### 4e. Trailing-slash inconsistency in schema URLs
- **Where:** `BRAND.url = "https://vlx.ai"` (no trailing slash, `constants.ts:5`) used in breadcrumb "Home" (`metadata.ts:28`, `SuperTemplate.tsx:652`) vs `"https://vlx.ai/"` (trailing slash) in `organizationSchema`/`websiteSchema` (`schema.ts:14,61`).
- **Why:** same entity referenced with two different URLs; minor but avoidable inconsistency (and the site enforces `trailingSlash: true`).
- **Fix:** standardize on the trailing-slash form `https://vlx.ai/` everywhere (matches `trailingSlash: true`), or normalize `BRAND.url` and derive both.

### Correction prompt
```
Repo: Next.js 15 + Payload. Fix the following JSON-LD (structured data) issues.
Make ONLY schema/metadata changes; do not alter visible HTML. Report a diff per item.

1) Hub ItemList — src/app/(frontend)/digital-inspections-software/page.tsx:
   The CollectionPage currently lists its children via a "hasPart" array of WebPage
   nodes. Add an ItemList as the CollectionPage's mainEntity: one ListItem per entry
   in the existing HUB_CHILDREN array, with sequential 1-based "position" and the
   child "url" (absolute, https://vlx.ai/..., trailing slash). Keep the existing
   CollectionPage @id and isPartOf.

2) BlogPosting — src/app/(frontend)/blog/[slug]/page.tsx:
   Change the article JSON-LD "@type" from "Article" to "BlogPosting". Keep every
   other field (headline, author, publisher @id, datePublished, mainEntityOfPage) and
   the existing BreadcrumbList unchanged.

3) Entity linking — src/app/(frontend)/integrations/[slug]/page.tsx and
   src/app/(frontend)/kypit/page.tsx:
   These emit a SoftwareApplication/Organization WITHOUT an @id. Make them reference
   the canonical nodes by @id: use "https://vlx.ai/#software" for the SoftwareApplication
   and provider/publisher "@id": "https://vlx.ai/#organization" for the Organization,
   mirroring how src/app/(frontend)/countit/page.tsx already does it. Do not create new
   unlinked duplicate entities.

4) AggregateOffer price — src/lib/schema.ts (softwareApplicationSchema, ~lines 81-88):
   lowPrice is "35" and highPrice "55", but PRICING in src/lib/constants.ts has annual
   prices 29 and 47. Set lowPrice to the true lowest available ("29") and highPrice to
   the true highest, keep priceCurrency "USD". If simple, expose both monthly and
   annual offers.

5) Trailing-slash consistency — src/lib/constants.ts (BRAND.url) / src/lib/metadata.ts /
   src/lib/schema.ts / src/components/sections/SuperTemplate.tsx:
   The site uses trailingSlash: true, but BRAND.url is "https://vlx.ai" (no slash) while
   organizationSchema/websiteSchema use "https://vlx.ai/". Standardize ALL schema URLs
   for the site root on "https://vlx.ai/" (with trailing slash). Update the breadcrumb
   "Home" item accordingly.

After all changes: run `npm run build`, then validate the emitted JSON-LD for the
homepage, the hub, a blog post, an integration page, and kypit against
https://validator.schema.org/ (or paste each JSON-LD block). Confirm no @id collisions
and no numberOfItems/itemListElement mismatches.
```

---

## Priority & sequencing

| # | Item | Severity | When | Requires rebuild? |
|---|---|---|---|---|
| 1 | Prod config / Cognito + `NEXT_PUBLIC_SITE_URL` | High | **Before/at go-live** | Yes (env var) + infra (ALB) |
| 2 | oil-and-gas slug | Medium | Before go-live (URL is permanent) | Yes |
| 3 | Blog schema slice | Medium | Before/at go-live | Yes |
| 4 | Structured data gaps (4a–4e) | Medium | Week 1–4 (4d/4e can be pre-launch, cheap) | Yes |

All are code/config changes deployed via the normal ECS build pipeline — none are content edits. Re-run the site build after each and verify with the steps in each prompt.
