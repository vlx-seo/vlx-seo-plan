# VLX Security Audit — Remediation Prompts (2026-07-14)

Companion remediation guide to the consolidated Application Security Testing report (Hans-review code review + dev.vlx.ai site assessment). Each finding below includes a **ready-to-paste fix prompt** you can hand to a developer or a coding assistant working in the `Visualogyx/VLX-Marketing` repo, plus **acceptance criteria** to verify the fix.

- **Scope:** 18 findings — 0 Critical, 1 High, 4 Medium, 13 Low. All Open.
- **Order:** fix High → Medium → Low.
- **Note:** these are defensive remediation instructions. Exploit steps / proof-of-concept are intentionally excluded (see the private full report).
- **Verification:** after applying, run `npm run build`, `npm run lint`, and re-test the affected flow.

---

## HIGH

### O-01 — Privilege escalation: editor can self-promote to admin
**Fix prompt:**
> In `src/collections/Users.ts`, the collection `update` access lets a non-admin update their own record, but the `role` select field has no field-level access control — so an editor can set their own `role` to `admin`. Add field-level `access` to the `role` field so only admins can create or update it:
> ```ts
> {
>   name: 'role',
>   type: 'select',
>   options: [
>     { label: 'Admin', value: 'admin' },
>     { label: 'Editor', value: 'editor' },
>   ],
>   defaultValue: 'editor',
>   required: true,
>   access: {
>     update: ({ req }) => req.user?.role === 'admin',
>     create: ({ req }) => req.user?.role === 'admin',
>   },
> }
> ```
> Do not change the collection-level `update` (self-service profile editing should still work). Keep all other fields editable by the record owner.

**Acceptance criteria:** an `editor`-role user cannot change their own or anyone's `role` via the admin UI or `PATCH /api/users/:id` (the field is silently ignored / rejected); an `admin` still can.

---

## MEDIUM

### O-02 — Enforce Content-Security-Policy (currently Report-Only)
**Fix prompt:**
> In `next.config.ts`, the `headers()` function emits `Content-Security-Policy-Report-Only` with `'unsafe-inline'` and `'unsafe-eval'` in `script-src` and no reporting endpoint. Harden and enforce it: (1) add a `report-to`/`report-uri` directive pointing at a violation-collection endpoint; (2) refactor inline GA/PostHog/Meta bootstrap scripts to use a per-request nonce (or move to external files) and remove `'unsafe-inline'`/`'unsafe-eval'` from `script-src`; (3) after validating with report-only data, rename the header key from `Content-Security-Policy-Report-Only` to `Content-Security-Policy`. Keep the existing good directives (`object-src 'none'`, `base-uri 'self'`, `form-action 'self'`, `frame-ancestors 'none'`).

**Acceptance criteria:** production responses carry an enforcing `Content-Security-Policy` header with no `unsafe-inline`/`unsafe-eval` in `script-src`; the site functions with no console CSP violations.

### O-03 — Add rate limiting to public form endpoints
**Fix prompt:**
> The route handlers `src/app/(frontend)/api/contact/route.ts` and `src/app/(frontend)/api/demo-request/route.ts` send two emails + a Slack notification per request with no rate limiting. A `rateLimit()` helper already exists at `src/lib/rate-limit.ts:38` but is never imported. Wire it into both handlers: key the limit on the client IP (`request.headers.get('x-forwarded-for')`), return HTTP `429` with a generic message when the limit is exceeded, and add a short per-email cooldown before sending the confirmation email. Because the app runs on Vercel serverless (per-instance memory), back the limiter with a shared store (e.g. Upstash/Redis) rather than the in-memory map.

**Acceptance criteria:** rapid repeated submissions from the same IP/email receive `429` after the threshold; a shared store is used so the limit holds across serverless instances.

### O-04 — Escape CMS values injected into JSON-LD (stored XSS)
**Fix prompt:**
> JSON-LD is injected via `dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}` in `src/app/(frontend)/blog/[slug]/page.tsx:177-201` and `src/lib/content-type-post.tsx:158-194`, embedding CMS-controlled fields (`title`, `excerpt`, `seoDescription`, `author`). `JSON.stringify` does not escape `<`, so a value containing `</script>` can break out of the script element. Create a shared helper that escapes the serialized output and use it at every `application/ld+json` sink:
> ```ts
> export const toJsonLd = (obj: unknown) =>
>   JSON.stringify(obj).replace(/</g, '\\u003c').replace(/>/g, '\\u003e').replace(/&/g, '\\u0026');
> ```
> Replace `JSON.stringify(...)` with `toJsonLd(...)` in every JSON-LD `dangerouslySetInnerHTML` (also `src/lib/schema.ts` and `src/components/sections/SuperTemplate.tsx` for defense-in-depth).

**Acceptance criteria:** a post whose title contains `</script><script>alert(1)</script>` renders the sequence inertly inside the JSON-LD block with no script execution.

### O-05 — Harden CMS (Payload) authentication configuration
**Fix prompt:**
> In `src/collections/Users.ts`, the `auth: true` config has no hardening. Replace it with an options object: `auth: { maxLoginAttempts: 5, lockTime: 600000, tokenExpiration: 7200, verify: true }` and add a password-strength `validate` (or Payload password rule) requiring a minimum length and complexity. Restrict admin-panel access to privileged roles via `admin.access` where appropriate. Ensure email verification is enabled for new users.

**Acceptance criteria:** weak passwords are rejected; repeated failed logins lock the account; sessions expire; new accounts require email verification.

---

## LOW

### O-06 — Add HSTS at the load-balancer edge
**Fix prompt:**
> The application sets HSTS, but the AWS ALB pre-authentication `302` redirects to Cognito do not carry `Strict-Transport-Security`. Configure the ALB (or the fronting CDN) to add `Strict-Transport-Security: max-age=63072000; includeSubDomains; preload` to all responses, including redirects.

**Acceptance criteria:** `curl -I https://dev.vlx.ai/` (the 302) includes the HSTS header.

### O-07 — Harden draft-preview secret handling
**Fix prompt:**
> In `src/app/(frontend)/api/draft/route.ts` (and the preview URL builders in `src/collections/Posts.ts` and `src/collections/CaseStudies.ts`), the preview secret is passed in the query string, compared with `!==`, and the `collection` query param is forwarded to `payload.find()` with default `overrideAccess: true`. Change to: accept the secret via an `Authorization` header or POST body; compare with `crypto.timingSafeEqual`; allow-list `collection` against the known preview collections and reject anything else with `400`; pass `overrideAccess: false`. Add basic rate limiting.

**Acceptance criteria:** the secret no longer appears in the URL; an invalid `collection` returns `400`; comparison is constant-time.

### O-08 — Make CAPTCHA fail closed outside production
**Fix prompt:**
> In `src/lib/turnstile.ts:16-18`, when `TURNSTILE_SECRET_KEY` is unset and `NODE_ENV === 'development'`, verification returns `true`. Gate this bypass behind an explicit `process.env.ALLOW_INSECURE_TURNSTILE === '1'` flag instead of `NODE_ENV`, so a misconfigured preview/staging build cannot silently disable the CAPTCHA. Also forward the client IP (`remoteip`) to Cloudflare's `siteverify`.

**Acceptance criteria:** without the explicit flag, a missing secret causes verification to fail; `remoteip` is sent to `siteverify`.

### O-09 — Move the hard-coded IndexNow key to env
**Fix prompt:**
> In `src/app/(frontend)/api/indexnow/route.ts:7`, the `INDEXNOW_KEY` constant is hard-coded. Move it to `process.env.INDEXNOW_KEY`, rotate the key in Bing Webmaster Tools, and rename the public `public/<key>.txt` file to match the new key. Remove the literal from source (it remains in git history — rotation is what mitigates it).

**Acceptance criteria:** no key literal in source; IndexNow submissions still succeed with the env-provided key.

### O-10 — Stop reflecting upstream errors; guard JSON parsing
**Fix prompt:**
> In `src/app/(frontend)/api/indexnow/route.ts` (lines ~33 and ~63-66), the handler returns the raw upstream response body and calls `await request.json()` without a guard. Wrap `request.json()` in try/catch returning `400` on malformed input; on upstream failure, log the detail server-side and return a generic `502` without the upstream body.

**Acceptance criteria:** malformed JSON returns `400`; upstream errors return a generic `502` with no third-party internals.

### O-11 — Fail fast on missing critical secrets
**Fix prompt:**
> In `payload.config.ts` (lines ~24, ~41, ~56), `PAYLOAD_SECRET`, `DATABASE_URL` and `BLOB_READ_WRITE_TOKEN` fall back to `''`. Replace the `|| ''` fallbacks with a startup assertion that throws a clear error when any of these is missing, so the app never boots with an empty signing secret.

**Acceptance criteria:** starting the app without `PAYLOAD_SECRET`/`DATABASE_URL` throws immediately with a descriptive message.

### O-12 — Do not print API-key characters in the test script
**Fix prompt:**
> In `scripts/test-resend.mjs:30`, the script prints the first 6 characters of `RESEND_API_KEY`. Change it to print only a presence indicator, e.g. `console.log('RESEND_API_KEY:', RESEND_API_KEY ? 'set' : '(not set)')`.

**Acceptance criteria:** the script never prints any characters of the key.

### O-13 — (Optional) restrict public read on Media/Categories
**Fix prompt:**
> `src/collections/Categories.ts:17` and `src/collections/Media.ts:17` use `read: () => true`. This is acceptable for public marketing data, but Media has no publish gating, so assets attached only to unpublished drafts are enumerable. If pre-launch assets must stay private, add a `read` access filter (e.g. restrict draft-only media to authenticated users) or store draft media separately.

**Acceptance criteria:** if adopted, unpublished/draft-only media is not readable anonymously; published assets remain public.

### O-14 — Update the X-XSS-Protection header value
**Fix prompt:**
> In `next.config.ts:34`, `X-XSS-Protection: 1; mode=block` is deprecated. Once the enforcing CSP (O-02) is shipped, change the value to `0` (per current OWASP guidance) or remove the header.

**Acceptance criteria:** header value is `0` (or removed) and an enforcing CSP is present.

### O-15 — Render heading emphasis as React nodes, not raw HTML
**Fix prompt:**
> Several components build heading HTML by string interpolation and inject it via `dangerouslySetInnerHTML`: `src/components/ui/SectionHeading.tsx:26-45`, `src/components/sections/SuperTemplate.tsx:1018,1865`, `IndustryTemplate.tsx:232`, `ProductFeatureSection.tsx:76`, `UseCaseTemplate.tsx:134`. Refactor to render the accented word as a real `<span className="text-brand">{accentWord}</span>` React node instead of injecting HTML. If HTML injection must remain, HTML-escape the non-accent text.

**Acceptance criteria:** no heading uses `dangerouslySetInnerHTML`; accent styling still renders.

### O-16 — Upgrade the outdated dev dependency
**Fix prompt:**
> `package.json` pins `puppeteer: ^22` (dev-only, used by `scripts/scrape-vlx.js`). Upgrade to the current major (or replace with a maintained alternative), then run `npm audit` and resolve any remaining dev-chain advisories.

**Acceptance criteria:** `npm audit` reports no high/critical advisories in the dev chain; scraping scripts still run.

### O-17 — Suppress/normalize the Server header at the edge
**Fix prompt:**
> Edge responses expose `Server: awselb/2.0`. Where the ALB/CDN configuration allows, suppress or normalize the `Server` response header to reduce infrastructure fingerprinting.

**Acceptance criteria:** `curl -I https://dev.vlx.ai/` no longer discloses `awselb/2.0` (or shows a generic value).

### O-18 — Set SameSite on the ALB auth cookie
**Fix prompt:**
> The `AWSALBAuthNonce` cookie is `Secure; HttpOnly` but has no `SameSite`. In the ALB authenticate-oidc configuration, set `SameSite` explicitly (e.g. `Lax`) on the session/auth cookie.

**Acceptance criteria:** the ALB auth cookie carries an explicit `SameSite` attribute.

---

## Suggested remediation order

1. **O-01** (High) — before any launch.
2. **O-02, O-03, O-04, O-05** (Medium).
3. **O-06 – O-18** (Low), batching header/config items (O-06, O-14, O-17, O-18) and code-hygiene items (O-09 – O-12, O-15, O-16) together.

After remediation, request a reassessment (retest) to confirm status transitions from **Open** to **Closed**.
