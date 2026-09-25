# Content Improvement Proposal — 5 VLX Blog Posts

**2026-09-24 · VLX SEO**

## How to read this

This is the page-by-page content spec for the 5 posts. Main keywords and the reasoning behind them were locked in the companion "Keywords principales" doc; here we go one level down: exact title/meta/H1, the heading outline, the FAQ block, the content gaps to close, and where each post links to VLX Home.

Each page section follows the same shape: **Current problems → Keyword map → Metadata (title / meta / H1) → Outline → FAQ → Content gaps → Internal links.**

## Changes that apply to all 5 pages

- **FAQ schema:** every page gets an FAQPage JSON-LD block matching its on-page FAQ. Three of the posts already have FAQs in the copy but no schema.
- **Author / E-E-A-T:** today all posts are bylined "VLX Team" marked up as Person, which is the wrong schema type. Keep the VLX byline — just change the markup from Person to Organization.
- **Downloadable PDF / template:** four of the five pages should offer the checklist as a PDF or a VLX Public Template. Several already promise a "downloadable PDF" in the copy without delivering one — either publish the asset or remove the promise.
- **VLX Home linking (future):** every home-inspection post links to the new VLX Home page once it's live. Until then the link target is a placeholder; activate at launch so we don't create broken or Cognito-gated links.
- **"Continue Reading" block:** today it's identical across pages (seniors / seniors / restaurants) and off-topic. Replace with 3 topically related posts per page.
- **Redirected internal links:** replace the `/blog/…` and `/construction/` links that currently 308-redirect with their final `/checklists/…` and `/digital-inspections-software/…` URLs.
- **Title/H1 hygiene:** no filler ("Ultimate", "Now", "Comprehensive Guide"), main keyword near the front, and fix the "14 Spets" typo on the pre-drywall title.

---

## Page 1 — Termite Inspection (California, mobile homes)

`/blog/is-termite-inspection-required-in-california-for-mobile-homes-a-comprehensive-guide/` · **Full rewrite** (body is currently empty).

**Current problems:** the body content is gone — only H1, 5 takeaways and the CTA remain (~270 words). "Continue Reading" links to unrelated posts (QMS, audits, collateral). Category is "Industry Trends"; should be a home-inspection/checklist category. Title/description read as keyword-stuffed.

**Keyword map**
- Main: `termite inspection mobile home california`
- Secondary (into H2/H3): termite clearance letter (170), fha termite inspection (140), section 1 termite clearance (110), va termite inspection requirements (90), wood destroying pest inspection (30), mobile home termite inspection (10).
- Secondary (into FAQ): is termite inspection required in california (10), how long is a termite inspection good for (110), who pays for termite inspection in california (20), termite inspection cost california (20).
- Do NOT target: termite inspection california (390) — local/service intent (map pack + ads).

**Metadata**
- Title (49): Termite Inspection for Mobile Homes in California
- Meta (154): Is a termite inspection required for mobile and manufactured homes in California? Rules by loan type (FHA, VA), clearance letters, who pays, and validity.
- H1: Termite Inspection for Mobile & Manufactured Homes in California

**Outline**
- H2: Is a termite inspection required in California? (the direct answer up top)
- H2: Rules by loan type — Conventional vs FHA vs VA (comparison table)
- H2: What a WDO report covers — Section 1 vs Section 2 (B&P Code §8516, Structural Pest Control Board)
- H2: The termite clearance letter — what it is and how long it lasts (90 days–6 months)
- H2: What's different about mobile / manufactured homes (piers, skirting, belly wrap, underside of a raised foundation) — the differentiator generic CA content misses
- H2: Who pays, and disclosure obligations
- H2: How VLX digitizes the WDO / termite inspection (product tie-in + CTA)

**FAQ (with FAQPage schema)**
- Do mobile homes need to be treated for termites?
- Is a termite inspection required in California?
- Does the buyer or seller pay for termite inspection in California?
- How much does a termite inspection cost in California?
- Can I do my own termite inspection?

**Content gaps to close:** loan-type requirements table; Section 1 vs 2 explained with the statute; who pays + disclosure; clearance validity; and the mobile/manufactured-specific inspection points (the unique angle).

**Internal links:** out to `/blog/mobile-home-inspection-checklist/` (final URL, not the 308) and to VLX Home (placeholder until launch). "Continue Reading" → mobile-home checklist, house-insurance checklist, home-safety checklist.

---

## Page 2 — Mold Inspection Checklist

`/checklists/mold-inspection-checklist/` · **Optimize existing** (already ~1,150 words).

**Current problems:** promises a "mold inspection checklist PDF" download that doesn't exist. Two internal links 308-redirect. FAQs have no schema and one H3 starts with a stray "2)". Mentions EPA but cites no source.

**Keyword map**
- Main: `mold inspection checklist`
- Secondary (into H2/H3): signs of mold in house (5,400), how to test for mold (5,400), how to check for mold (1,900), black mold inspection (880), mold remediation checklist (260), mold inspection process (30), diy mold inspection (30).
- Secondary (into FAQ): mold inspection cost (4,400), how to inspect for mold (90).
- Download variants: mold inspection checklist pdf (20), free / residential / home mold inspection checklist.

**Metadata**
- Title (50): Mold Inspection Checklist: Room-by-Room Home Guide
- Meta (142): Mold inspection checklist for your home: check bathrooms, attics, basements and HVAC, measure humidity, and know when to call a pro. Free PDF.
- H1: Mold Inspection Checklist for Homes

**Outline**
- H2: What a mold inspection is / the process (step by step)
- H2: Signs of mold in a house (captures the 5,400 term)
- H2: Room-by-room checklist — bathrooms, kitchen cabinets, attic, basement, HVAC (the gap: top-3 results are generic PDFs)
- H2: How to test for mold — DIY kits vs moisture meter / humidistat → H3: Black mold — when it's serious
- H2: When to call a professional (+ cost)
- H2: Remediation checklist — what to do after you find mold
- H2: Digitize your mold inspection with VLX (+ downloadable checklist CTA)

**FAQ (with FAQPage schema)**
- How do I prepare for a mold inspection?
- What are the first signs of mold in a house?
- What time of year is worst for mold?
- How much does a mold inspection cost?

**Content gaps to close:** room-by-room detail; how to measure humidity; a real downloadable PDF/interactive checklist; when to call a pro + cost; remediation steps. Cite EPA/CDC where mold health risk is mentioned.

**Internal links:** fix the two 308s to final URLs; link out to VLX Home (placeholder). "Continue Reading" → home-safety checklist, house-insurance checklist, rental-inspection checklist.

---

## Page 3 — Pre-Drywall Inspection Checklist

`/checklists/pre-drywall-inspection-checklist/` · **Optimize existing** (already ~1,175 words, 14 points).

**Current problems:** title typo — "14 Spets" instead of "Steps" (also shows in og:title/social). H1 is over-long. Says "pre drywall inspection checklist pdf" 3× with no PDF offered. Links to `/construction/` (308). FAQs without schema.

**Keyword map**
- Main: `pre drywall inspection checklist`
- Parent term (H1/intro): pre drywall inspection (1,000)
- Secondary (into H2/H3): framing inspection checklist (260), rough in inspection checklist (90), new home inspection checklist (210), pre drywall walkthrough (20), what is a pre drywall inspection (50).
- Secondary (into FAQ): pre drywall inspection cost (720).
- Download variants: pre drywall inspection checklist pdf (70), pre drywall inspection checklist template (10).

**Metadata**
- Title (52): Pre-Drywall Inspection Checklist: 14 Points to Check
- Meta (141): Pre-drywall inspection checklist for new construction: framing, headers, MEP rough-ins and insulation to verify before walls close. Free PDF.
- H1: Pre-Drywall Inspection Checklist for New Construction

**Outline** (keep the 14-point structure — it's the strength)
- H2: What is a pre-drywall inspection? (definition + timing, before insulation/drywall)
- H2: The 14-point checklist — grouped by trade
  - H3: Framing (frames, headers, beams, bottom plates, rim boards)
  - H3: Structural connectors (joist hangers, hurricane clips, metal beams)
  - H3: Envelope (windows, exterior doors, shims, siding/masonry veneer)
  - H3: MEP rough-in (HVAC, electrical, plumbing)
  - H3: Fire blocking / firestopping + nail plates over pipes (gap)
  - H3: Insulation & subfloor
- H2: Who attends and what to expect after
- H2: Is it worth it? cost + timing (captures the 720 term)
- H2: Document every wall with photos before close-up — with VLX (CTA)

**FAQ (with FAQPage schema)**
- What is included in a pre-drywall inspection?
- How long does a pre-drywall inspection take?
- Is a pre-drywall inspection worth it?
- What needs to be done before drywall?

**Content gaps to close:** fire blocking + nail plates; photo documentation before close-up; who attends + timing; cost/worth-it section; a real PDF/template.

**Internal links:** fix `/construction/` to its final URL; link out to VLX Home (placeholder). Keep intent informational. "Continue Reading" → new-construction / framing / virtual-inspections posts.

---

## Page 4 — Fire Safety Inspection (home + workplace)

`/checklists/a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces/` · **Optimize existing** (~2,580 words). **Highest-opportunity page:** main keyword 1,000 vol / KD 0, and GSC shows position 37.2 with impressions but 0 clicks — dormant potential.

**Current problems:** meta description is 225 chars (truncates ~155). Four H2s are empty labels — the outline reads like a duplicated table of contents. Old `app.vlx.ai/SignUp?…&_gl=…` link with a 2024 tracking param. Mentions NFPA but links no source. No FAQ.

**Keyword map**
- Main: `fire safety inspection`
- Secondary (into H2/H3): fire safety checklist (880), fire prevention checklist (880), fire inspection checklist (480), fire safety inspection checklist (260), home fire safety checklist (140), fire door inspection checklist (170), osha fire safety checklist (30), commercial fire inspection checklist (30), workplace fire safety checklist (10).
- Entities: nfpa 10 requirements (10) — NFPA 10 (extinguishers), 101 (Life Safety Code), 72 (alarms).
- Exclude as target: fire extinguisher inspection requirements (260) — already covered by the dedicated extinguisher blog; link only.

**Metadata**
- Title (53): Fire Safety Inspection Checklist for Home & Workplace
- Meta (150): Fire safety inspection guide for homes and workplaces: extinguishers, alarms, exits, electrical and NFPA checks, plus how often and who performs them.
- H1: Fire Safety Inspection Guide for Homes and Workplaces

**Outline** (collapse the empty label-H2s into real content)
- H2: What a fire safety inspection is and who performs it
- H2: Home fire safety checklist
- H2: Workplace fire safety checklist (OSHA + commercial)
- H2: The core checks — detection & alarms, extinguishers, exits/egress, electrical, fire doors
  - H3: Smoke / CO alarms — testing and frequency
  - H3: Exits, exit signs and clear egress paths (gap)
  - H3: Electrical hazards (extension cords, panel access)
  - H3: NFPA references (10, 72, 101)
- H2: After the inspection — reading the report, prioritizing fixes, recordkeeping
- H2: How often, and how much it costs
- H2: Run fire safety inspections in VLX (CTA)

**FAQ (new — with FAQPage schema)**
- What does a fire safety inspection consist of?
- What happens during a fire safety inspection?
- How much does an annual fire inspection cost?
- Who can perform fire inspections?
- What are the NFPA 10 inspection requirements?

**Content gaps to close:** clear home vs workplace split (differentiator — most competitors are business-only); NFPA citations; egress/exit signage; alarm test frequency; electrical hazards; cost/frequency/who-can-perform; separate home + workplace PDFs.

**Internal links:** replace the stale SignUp link; link to the extinguisher blog for that sub-topic; link out to VLX Home (placeholder). "Continue Reading" → extinguisher-frequency, home-safety checklist, health-inspection (workplace) posts.

---

## Page 5 — Home Safety Inspection Checklist (RE-SCOPE to general)

`/checklists/home-safety-inspection-checklist/` · **Re-scope** from seniors → general.

**Current problems:** ~91% duplicate of the seniors checklist (same H1, they even link to each other) → the two cannibalize. Content is padded with irrelevant rooms for a safety checklist ("Prayer Room", "Playroom", "Home Gym"). FAQs without schema. KD 28 = the hardest of the set.

> **Depends on the open decision: re-scope vs consolidate.** This spec assumes re-scope. If we consolidate instead, this URL 301s to 5b and this section is dropped.

**Keyword map**
- Main: `home safety checklist` (590, KD 28)
- Secondary (into H2/H3): home safety assessment (390), home safety inspection (210), home safety evaluation (170), home safety audit (20), home safety checklist for parents (30, childproofing).
- Exact-match variant (H1/URL term): home safety inspection checklist (30).
- Download variants: home safety checklist pdf (50), printable home safety checklist (30).

**Metadata**
- Title (52): Home Safety Inspection Checklist: Room-by-Room Guide
- Meta (149): Home safety checklist to spot hazards room by room: smoke and CO alarms, electrical, stairs, kitchen and bathroom. Free printable PDF for every home.
- H1: Home Safety Inspection Checklist for Every Home

**Outline** (general audience — drop the filler rooms)
- H2: Why a home safety checklist matters
- H2: Whole-home essentials — smoke/CO alarms, electrical & GFCI, water heater at 120°F
- H2: Room-by-room checklist — kitchen, bathroom, stairs/hallways, bedrooms, living areas, garage/basement
- H2: Childproofing basics (captures home safety checklist for parents)
- H2: Exterior — roof, gutters, walkways, lighting
- H2: How often to run a home safety audit
- H2: Run your home safety inspection in VLX (CTA + printable PDF)

**FAQ (with FAQPage schema)**
- What should be in a home safety checklist?
- What are 5 very important things inspected in a home inspection?
- What things will fail a home inspection?
- What is the biggest red flag in a home inspection?

**Content gaps to close:** room-by-room for ALL audiences (not just seniors); alarms/electrical/GFCI; stairs & falls; water heater temp; exterior; childproofing section; printable PDF + annual cadence. Note: some PAA is about purchase/property inspection — keep the page anchored on home safety, not buying.

**Internal links:** cross-link to the seniors page (5b) as the specialized version; link out to VLX Home (placeholder). "Continue Reading" → seniors checklist, fire-safety, house-insurance checklist.

---

## Page 5b — Home Safety Checklist for Seniors

`/checklists/home-safety-checklist-for-seniors/` · **Keep senior focus** (the counterpart to the re-scope).

**Current problems:** shares the senior angle with page 5 today, hence the cannibalization. Once page 5 goes general, this one owns the senior intent cleanly (KD 3 = very easy to rank). Keep and sharpen the unique "can they still live alone?" framing.

**Keyword map**
- Main: `home safety checklist for seniors` (320, KD 3)
- Same-intent variants: home safety checklist for elderly (320), senior home safety checklist (320), elderly home safety checklist (320), home safety checklist for older adults (20).
- Secondary (into H2/H3): aging in place checklist (320), fall prevention checklist (70), aging in place home safety (10).
- Download variants: printable home safety checklist for seniors (20), home safety checklist for seniors pdf (10).

**Metadata**
- Title (49): Home Safety Checklist for Seniors: Aging in Place
- Meta (146): Home safety checklist for seniors: room-by-room fall prevention, grab bars, lighting and bathroom safety to help older adults live at home safely.
- H1: Home Safety Checklist for Seniors (Aging in Place)

**Outline**
- H2: Can they still live safely at home? — the decision framework (unique angle)
- H2: Fall prevention room by room (bathroom = highest-risk zone)
  - H3: Grab bars mounted to a stud; non-slip mats; raised toilet / shower chair
  - H3: Lighting + sensor nightlights; clear bed→bathroom path
- H2: Aging-in-place checklist — mobility, access, everyday tasks
- H2: Medical alert devices
- H2: Does Medicare cover a home safety assessment? (OT assessments, dementia considerations)
- H2: Run the senior home safety assessment in VLX (CTA + printable PDF)

**FAQ (with FAQPage schema)**
- What are some home safety tips for seniors?
- Does Medicare cover home safety assessments?
- What should a 70-year-old be doing every day at home?
- What is included in a home safety assessment for the elderly?

**Content gaps to close:** fall prevention emphasis; grab bars to stud; bathroom as top risk; sensor lighting; medical alert; the "can they live alone?" decision framework; Medicare/OT coverage and dementia notes.

**Internal links:** cross-link to the general page (5) as the broader version; link out to VLX Home (placeholder). "Continue Reading" → general home-safety, house-insurance checklist, fire-safety.

---

## Rollout order & dependencies

Suggested order (by opportunity, easy wins first):

1. **Page 3 — Pre-drywall:** ship the "Spets" title fix now; it's a visible error in the SERP. Then the rest.
2. **Page 4 — Fire safety:** highest upside (1,000 vol, already ranking at 37 with 0 clicks). Trim meta, fill empty H2s, add FAQ.
3. **Page 5 / 5b — Home safety:** do together once the re-scope vs consolidate decision is made — they must ship as a pair to resolve the cannibalization.
4. **Page 2 — Mold:** add room-by-room + PDF + FAQ schema.
5. **Page 1 — Termite:** full rewrite — most work, lowest volume, so last.

## Blocking decisions / inputs

- **Home safety (P5):** re-scope to general vs consolidate with a 301. The whole P5/5b spec hinges on this.
- **VLX Home URL:** needed to activate the outbound links. Until then, placeholders.
- **PDFs / templates:** confirm which checklists exist as downloadable assets (or create them) before the copy promises them — affects P2, P3, P5, P5b.
- **Author / E-E-A-T:** change the byline markup from Person to Organization (byline stays "VLX Team") — applies to all five.
- **GSC data:** pull real current positions (3 & 12 months) per URL to confirm this priority order.

---

*Keyword data: Ranki (US, English) + Google US SERP, 2026-09-24. Main-keyword rationale lives in the companion "Keywords principales" document.*
