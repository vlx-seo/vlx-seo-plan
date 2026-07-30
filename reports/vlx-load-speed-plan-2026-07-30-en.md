# VLX — Mobile load speed: what we found and what we recommend

**Date:** 30 July 2026
**Author:** Laura Ceballos — Software Craft CR
**Scope:** vlx.ai production, mobile responsiveness

---

## Summary

| # | Item | Owner | Decision needed? |
|---|---|---|---|
| 0 | **Desktop already passes — this is a mobile-only problem** | Everyone | No — context |
| 1 | The mobile numbers aren't final yet | Hans | No — context |
| 2 | **Brotli compression is off → 18% smaller downloads, measured. Highest-value item here.** | Vivek | No — just enable it |
| 3 | HTTP/3 — please confirm whether it's on | Vivek | No — confirm |
| 4 | Legacy browser code — checked and ruled out, no action | — | No — closed |
| 5 | PostHog loads during page load instead of after | Abe | No — we'll do it |
| 6 | Session replay records what visitors type into forms | Hans | **Yes — privacy** |

Items 2 and 3 are infrastructure and don't touch the codebase at all. Item 5 is code. Item 6 is
a one-line code change but the call isn't ours. **Only item 6 needs a decision from anyone.**

---

## 0. Why this is a mobile problem, and desktop needs nothing

*(Context for everyone — this is the reason every other section talks about phones)*

Your own screenshots answer this, and the answer is unusually clear. Same site, same day,
same 68 URLs:

| | Mobile | Desktop |
|---|---|---|
| **Core Web Vitals (real users)** | **Failed** | **Passed** ✅ |
| INP — responsiveness to taps/clicks | **221 ms** ⚠️ | 95 ms ✅ |
| LCP — main content appears | 1.9 s ✅ | 1.5 s ✅ |
| CLS — layout stability | 0.01 ✅ | 0.08 ✅ |
| TTFB — server response | 0.9 s ⚠️ | 1.1 s ⚠️ |
| Search Console URL verdicts | 0 good, **68 need improvement** | **68 good**, 0 need improvement |
| Lighthouse score (lab simulation) | 70 | 72 |

Three things follow from this table, and they shape the whole document:

**1. Desktop already passes. There is nothing to fix there.** All 68 URLs are green in Search
Console and have been green all year. Any work aimed at desktop would be effort spent on
something that isn't broken.

**2. On mobile, INP is the *only* failing metric.** LCP and CLS are green with room to spare —
they're actually better on mobile than the desktop CLS. So "focus on mobile responsiveness"
isn't a choice among several problems. It's the one thing that's broken.

**3. The lab score and reality disagree, and that's the important part.** The Lighthouse
scores are almost the same — 70 mobile, 72 desktop. If you judged by those, you'd conclude
both platforms are equally mediocre and both need work. But the real-user verdicts are
*opposite*: desktop passes, mobile fails. **The simulation cannot tell those two apart.** That
is why we're not planning against the 70.

One exception worth noting, because it's the only metric that's amber on *both*: **TTFB, the
server response time.** That's infrastructure rather than page code, and it's exactly what the
compression and HTTP/3 items in sections 2 and 3 address. So that work benefits desktop too —
it's the one part of this plan that isn't mobile-only.

---

## 1. The mobile numbers aren't final yet

*(Context for Hans — no action needed)*

To be precise about which number is which, because both kinds appear in the same screenshot:

- **The 221 ms INP is real field data** — actual visitors, collected by Chrome. That one is
  genuine and it's what Google ranks on.
- **The 70 is a lab simulation** — one page load on a virtual throttled phone. Useful as a
  diagnostic, but as shown above it can't distinguish a passing platform from a failing one.

So the concern isn't that the 221 ms is wrong. It's *when* it was measured.

### There's also a timing problem

Google calculates that average over a **rolling 28-day window**. The new site launched on
**22 July**. So today's number blends roughly 3 days of the new site with 25 days of the old
WordPress site.

**The first clean reading of the new site arrives around 19 August.** Any conclusion drawn
before then is mostly measuring the previous site.

### The finding that changes the picture

We pulled the full 25-week history. **The mobile responsiveness problem did not start with
the new site:**

| When | What was in production | Responsiveness |
|---|---|---|
| February → early April | Old WordPress | normal |
| **18 April** | **Old WordPress** | **starts degrading** |
| Late April | Old WordPress | **worst point of the year** |
| May → June | Old WordPress | still poor |
| Today | New Next.js site | **better than the old site's worst point** |

The degradation began on **18 April**, three months before launch, with the old site live.
And the old site got measurably worse than where we are today.

**The new site also clearly improved two other things** Google measures: the time until the
main content appears dropped by about 35%, and server response time roughly halved.

**Bottom line:** the new site didn't cause this. It inherited an existing problem and
improved several things along the way. The remaining issue is worth fixing — we just
shouldn't judge the result until real data lands around 19 August.

---

## 2. Brotli compression is off — 18% smaller downloads, no code, no risk

*(For Vivek — infrastructure)*

We asked whether the caching-and-compression plugins that used to speed up the WordPress site
have an equivalent here. Two answers:

**Caching is already maxed out.** Pages are generated once and served from CloudFront with a
one-year cache instruction. That's more aggressive than any WordPress plugin. Nothing to
improve.

**Compression is only half done.** The server compresses with **gzip**. There's a newer
method, **brotli**, built by Google specifically for the web and supported by every modern
browser for years. We tested it and **the site isn't using it**: when a browser requests
brotli, the server returns the file uncompressed.

We compressed the site's actual production files with both methods to measure the real
difference — not an estimate:

| | gzip (today) | brotli | Difference |
|---|---:|---:|---:|
| 12 largest JavaScript files | 965 KB | 791 KB | **171 KB less (18.1%)** |

**Every visitor would download 18% less**, on every page, on every device.

- **No code change.** It's a setting in the CloudFront distribution.
- **No risk.** If a browser doesn't support brotli, the server keeps serving gzip
  automatically. Nothing breaks for anyone.
- **No cost** — it actually reduces egress traffic.

Reproduce it:

```bash
curl -s -H "Accept-Encoding: br"   -o /dev/null -w "%{size_download}\n" https://vlx.ai/digital-inspections-software/
curl -s -H "Accept-Encoding: gzip" -o /dev/null -w "%{size_download}\n" https://vlx.ai/digital-inspections-software/
```

Today the brotli request returns **149,211 bytes** (uncompressed) and the gzip request
returns **20,684 bytes**. Brotli should be the smaller of the two, not the larger.

**This is the best effort-to-result ratio in this document.**

---

## 3. Is HTTP/3 enabled?

*(For Vivek — confirmation)*

We couldn't verify this from our side. HTTP/3 reduces response time mainly on unstable mobile
connections, and **54% of the site's traffic is mobile**, so it's worth confirming. If it's
off in CloudFront, enabling it is another no-code, no-risk improvement.

---

## 4. Legacy browser code — we checked this and it's a non-issue

*(No action needed — recording it so nobody spends time on it later)*

This looked like a significant win and it isn't. Worth writing down so it doesn't get
re-proposed.

The build produces a 112,594-byte `polyfills` file — compatibility code that hand-writes modern
JavaScript features into older browsers. The obvious conclusion is that every visitor downloads
and executes 110 KB they mostly don't need, and that constraining which browsers we support
would remove it.

**We tested that instead of assuming it.** We set an explicit browser target of 2023-and-newer,
rebuilt, and the polyfills file came out *byte-for-byte identical* — same size, same content
hash. Total shared JavaScript didn't move either.

The reason is in how Next serves it:

```html
<script src="/_next/static/chunks/polyfills-....js" noModule="">
```

`noModule` means **any browser supporting ES modules ignores that file completely** — never
downloaded, never parsed, never executed. That's every browser since 2018. So a modern phone was
never paying for it, and it contributes nothing to the responsiveness problem. The file is also a
fixed Next asset rather than something generated from a browser-target list, which is why
constraining the target changed nothing.

**Conclusion: nothing to gain, nothing to decide, no risk to accept.** For the record, had it
been worth doing, twelve months of GA4 for the US and Canada showed the real constraint would
have been Windows 7 and 8 at 0.70% of traffic, with iOS 15-and-up covering 99.2% of iOS traffic.
Those numbers are sound — they just have nothing to spend themselves on here.

---

## 5. PostHog loads during page load instead of after

*(For Abe — no decision needed, we'll handle it)*

We measured what happens when someone taps a button, using real taps on a CPU-throttled
mobile browser. The result was clear:

**The site's own code is fast.** The handler that responds to a tap runs in **3 to 9
milliseconds**. The delay is that the phone is busy with something else and can't get to the
tap yet. It's a queue, not slowness. About two seconds in, the problem disappears entirely.

PostHog is the heaviest single thing loading in that window. The cause is in our code, not in
PostHog: it's imported statically in four files, and one of them (`src/lib/analytics.ts`) is
imported by 18 components including `CTABanner`, `FAQSection` and `SuperTemplate` — which
appear on nearly every page. That pulls PostHog into the initial bundle sitewide.

Also worth noting: `posthog-js/react` is imported to wrap the whole app in a provider, but
**nothing in the codebase uses the `usePostHog()` hook or that context**. It can be removed
outright.

**The fix loses no data.** Every tracking call happens in response to a user action, so
loading PostHog on demand captures exactly the same events. It just stops competing with the
page for the phone's attention. We'll put this in its own commit so it can be reverted
independently if anything looks off in your event stream.

---

## 6. Session replay is recording what visitors type into forms

*(For Hans — privacy decision)*

**Session replay stays.** We understand the team uses it to make UX decisions and prioritise
improvements, and that's worth more than a few milliseconds. We're accepting that cost
deliberately and finding speed elsewhere.

**But one part should be turned off.** The recording currently captures **the text visitors
type into forms**, including the demo request form.

We can disable **only the typed-text capture while keeping the full replay**. You'd still see
where people navigate, what they tap, where they hesitate and where they drop off —
everything that's useful for UX decisions. The only thing that stops being stored is the
literal content of what they type.

**One thing you should know:** the current configuration attempts to protect credit-card
fields, but **that protection isn't working.** Because of how it's written, only password
fields are actually masked — every other field is recorded in plain text. The fix covers all
fields, not just passwords.

Implementation note, since this is subtler than it looks: setting `maskAllInputs: true` is
**not sufficient on its own**. rrweb's masking logic delegates to `maskInputFn` when one is
defined, and the current function returns the raw text for anything that isn't a password. So
`maskAllInputs` must be set to `true` **and** the existing `maskInputFn` removed. Changing
only the boolean would leave everything recorded in the clear while appearing fixed.

---

## What we're doing next, regardless

- Moving PostHog out of the initial bundle (item 5) — no decision needed.
- Applying the form-privacy fix (item 6) once approved.
- Re-measuring after 19 August, when Google's first clean reading of the new site lands.

We'll report measured numbers, not estimates.

---

*Laura Ceballos · Software Craft CR · 30 July 2026*
