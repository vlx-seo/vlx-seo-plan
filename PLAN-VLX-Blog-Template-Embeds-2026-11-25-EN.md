# Blog Template Embeds — Fix & Implementation Plan

**Site:** vlx.ai · **Date:** 2026-11-25 · **Scope:** all 124 content URLs in the sitemap (76 /blog/, 34 /checklists/, 11 /use-case/, 2 /forms/, 1 /reports/)

---

## 1. Summary

- **5 posts have a broken template preview.** Readers see a raw text line `[Embedded content: https://app.vlx.ai/...]` instead of the interactive VLX checklist.
- **31 posts promise a checklist or template in their title but have none** (Priority A). 9 more inspection guides would benefit from one (Priority B).
- **12 posts show `[Image: ...]` as text** instead of an image. Same root cause.
- **No code change is required.** The "VLX Template Preview" block (PR #181 + #186) is already live in production. All fixes are done in the CMS. For the new templates, they also have to be created in the VLX app.

## 2. Root cause

During the WordPress → Next.js/Payload migration, `scripts/migrate-wordpress.ts` (≈ line 367) turned every `<iframe>` into a plain paragraph `[Embedded content: <src>]`, and every `<img>`/`<figure>` into `[Image: <alt or src>]`. The iframes were the app's template previews, so all of them were lost.

Verified live on 2026-11-25:

- All 5 template IDs still exist and render in the app.
- app.vlx.ai allows framing from vlx.ai (`frame-ancestors 'self' https://visualogyx.com https://vlx.ai ...`). A test iframe injected into a live vlx.ai post rendered the template correctly.
- **dev.vlx.ai is not in `frame-ancestors`**, so the embed shows as blocked on dev. See section 9.
- Today, 0 of the 124 URLs use the Template Preview block.

## 3. Standard embed pattern (use it on every post)

Follow the pattern the migrated posts already use, in this order, inside the post body:

1. **Label paragraph (bold):** `VLX App Preview – <Exact template name>`
2. **Block:** Add block → **VLX Template Preview**
   - **URL:** `https://app.vlx.ai/public-access/public-template-preview/<TEMPLATE_ID>?size=mobile`
   - **Height:** leave the default (960). Mobile previews never render shorter than 960.
   - **Title (accessible name):** `<Template name> template preview`
3. **CTA paragraph:** `Get started today! Sign up for free (no credit card required).` The link goes to `https://app.vlx.ai/login/?plan=ProfessionalTrial`.

**Placement rule:** put the embed right after the section that lists the checklist items, so the reader sees the interactive version of what they just read. Never place it above the first H2.

**Definition of done (per post):**
- The `[Embedded content` text is no longer in the page.
- The phone-frame preview renders on vlx.ai.
- The bold label matches the template name shown inside the preview.

## 4. Phase 1: Fix the 5 broken posts (Priority: P0, CMS only)

For each post: open it in the CMS, delete the paragraph `[Embedded content: ...]`, and insert the **VLX Template Preview** block in the same position with the URL below. Then publish.

| # | Post | Template name | Template ID | Extra action |
|---|------|---------------|-------------|--------------|
| 1 | /checklists/food-safety-checklist/ | Food Service Facility Inspection | `4d3a37b5-8941-4dcd-998a-25f06042220c` | Fix the label: rename "VLX App – Food Safety Facility Inspection Checklist" → "VLX App Preview – Food Service Facility Inspection". **App team:** this template's description talks about vehicle inspections; replace it with a food-service description. |
| 2 | /checklists/hotel-maintenance-checklist/ | Hotel Room Housekeeping Checklist | `5f4ee0c6-72d0-433e-a36d-341e703de65e` | **Wrong label:** it says "Gemba Walk Inspection Form". Rename to "VLX App Preview – Hotel Room Housekeeping Checklist". Long-term, build a dedicated *Hotel Maintenance Checklist* template (Phase 3) and swap it in. |
| 3 | /checklists/best-gemba-walk-checklist-by-experts-get-the-template/ | Gemba Walk Checklist | `a36819fb-56c7-4577-8e55-bad98ec6e356` | The H1 promises "Get The Template", so this is the most visible break. Fix first. |
| 4 | /checklists/understanding-californias-sb-326-and-sb-721-balcony-inspection-laws-what-you-need-to-know/ | SB 326 / 721 Balcony Inspection Checklist | `1186f76b-bf17-4f43-a8fb-f5f871b4751e` | — |
| 5 | /blog/why-container-inspections-are-crucial-for-freight-forwarders-and-iso-compliance/ | Container Inspection Form for International Providers | `c04e70f9-865f-4606-af5b-e02ec9019e2c` | — |

**Effort:** about 10 minutes per post, about 1 hour total.
**Owner:** CMS editor. App team for the food-service description.

### Alternative (engineering, optional)

A small renderer change in `src/components/blog/RichTextRenderer.tsx` could detect paragraphs whose only text is `[Embedded content: https://app.vlx.ai/...]` and render them as `TemplateEmbedBlock`. That fixes all 5 in one PR, with no CMS edits. **Recommendation: do it in the CMS.** It is quicker, it leaves no legacy hack in the code, and the label fixes in posts #1 and #2 have to be done by hand anyway.

## 5. Phase 2: Reuse existing templates (Priority: P1, CMS only)

These posts match a template that already exists, so no app work is needed.

| Post | Template | ID | Place after |
|------|----------|----|-------------|
| /use-case/the-essential-hotel-room-housekeeping-checklist-for-optimal-guest-satisfaction/ | Hotel Room Housekeeping Checklist | `5f4ee0c6-72d0-433e-a36d-341e703de65e` | The main checklist section |
| /forms/lean-manufacturing-gemba-walk/ | Gemba Walk Checklist | `a36819fb-56c7-4577-8e55-bad98ec6e356` | The section describing how to run the walk |

**Effort:** about 20 minutes total.

## 6. Phase 3: New templates for Priority A posts (P1–P2: app + CMS)

These posts' titles promise a checklist or template, but the page has nothing interactive.

### Workflow per post

1. **App team or content:** check the VLX template library for an existing match. If there is none, build the template in the VLX app using the post's checklist sections (listed below) as the section structure.
2. Publish the template's public preview and copy the link: `https://app.vlx.ai/public-access/public-template-preview/<ID>?size=mobile`.
3. Write the template name and ID into the tracking table (section 10).
4. **CMS editor:** add the embed using the standard pattern (section 3) after the anchor section.

### Template specs

Sections are taken from each post's own headings, so the template mirrors the article.

**Batch 3a: Vehicle / fleet (high commercial intent, shared building blocks)**

| Post | Template to build | Suggested sections | Place embed after |
|------|-------------------|--------------------|-------------------|
| /checklists/cdl-pre-trip-inspection-checklist/ | CDL Pre-Trip Inspection Checklist | Engine compartment · In-cab · Exterior/lights · Brakes · Coupling · Tires & wheels | "A Complete CDL Pre-Trip Inspection Checklist" |
| /checklists/truck-inspection-checklist/ | Truck Pre-Trip Inspection Checklist (15-min) | Pre-inspection · Exterior · Engine · Brakes · Tires · Reporting | "15-Minutes Pre-Trip Truck Inspection Checklist" |
| /checklists/how-to-develop-a-perfect-daily-inspection-checklist-for-forklift/ | Daily Forklift Inspection Checklist | Visual checks · Operational checks · Pre-operation sign-off | "Forklift Truck Daily Inspection Checklist" |
| /checklists/crane-inspection-checklist/ | Crane Inspection Checklist | Structure · Wire rope & hooks · Controls · Safety devices · Load test | The main checklist section |
| /checklists/pre-purchase-inspection-checklist/ | Used Vehicle Pre-Purchase Inspection | Exterior · Interior · Engine bay · Transmission & drivetrain · Undercarriage & suspension · Brakes · Test drive | "Comprehensive Pre-Purchase Inspection Checklist" |
| /use-case/a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/ | 15-Point Multi-Point Vehicle Inspection | Brakes · Tires · Fluids · Battery · Belts · Hoses · Filters · Lights · Wipers · Suspension · Exhaust… | "The Complete 15-Point Multi-Point Inspection Checklist" |

**Batch 3b: Home / residential / real estate (strongest match with VLX Home)**

| Post | Template to build | Suggested sections | Place embed after |
|------|-------------------|--------------------|-------------------|
| /checklists/pre-drywall-inspection-checklist/ | Pre-Drywall Inspection Checklist | Framing · Electrical rough-in · Plumbing rough-in · HVAC · Insulation/air sealing · Windows & doors | The main checklist section |
| /blog/mold-inspection-checklist/ | Mold Inspection Checklist (Room by Room) | Signs of mold · Bathrooms · Kitchen · Basement & crawl space · Attic · HVAC & laundry | "Room-by-room mold inspection checklist" |
| /checklists/home-safety-inspection-checklist/ | Home Safety Inspection (Room by Room) | Whole-home essentials · Kitchen · Bathroom · Stairs & hallways · Bedrooms · Living areas · Garage/basement/exterior | "Room-by-room checklist" |
| /checklists/home-safety-checklist-for-seniors/ | Senior Home Safety Assessment | Bathroom · Lighting & pathways · Stairs/floors/furniture · Access & everyday tasks · Medical alert | "Run the senior home safety assessment in VLX" (the post already announces it) |
| /checklists/mobile-home-inspection-checklist/ | Mobile Home Inspection Checklist | Exterior · Interior · Structural · Safety features | "Mobile Home Inspection Checklist" |
| /checklists/house-insurance-inspection-checklist/ | Home Insurance Inspection Checklist | Exterior · Interior · Systems & utilities · Safety & liability | "Best Home Insurance Inspection Checklist" |
| /checklists/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/ | Rental Property Inspection Checklist | Exterior · Common areas · Kitchen · Bathrooms · Bedrooms · Safety devices · Tenant notes | "Comprehensive Rental Inspection Checklist" |
| /checklists/what-to-look-for-when-renting-a-houses/ | Rental Walk-Through (Move-In/Move-Out) | Room-by-room condition · Appliances · Utilities · Photos & signatures | "Essential Items to Include in the Rental Inspection Checklist" |
| /checklists/checklist-for-annual-apartment-inspection/ | Annual Apartment Inspection Checklist | Safety devices · Plumbing · Electrical · HVAC · Appliances · Interior condition | "Essential Items in the Annual Apartment Inspection Checklist" |
| /checklists/commercial-building-inspection-checklist/ | Commercial Building Inspection Checklist | Structural · Electrical · Plumbing · HVAC · Fire safety · Accessibility · Exterior/roof | "A Complete Commercial Building Inspection Checklist" |
| /checklists/the-best-swimming-pool-inspection-checklist-2025-guide-to-ensure-pool-safety-and-compliance/ | Swimming Pool Safety Inspection | Barriers & gates · Water quality · Equipment · Drains & entrapment · Signage & rescue gear | The main checklist section |
| /checklists/best-furnace-maintenance-checklist/ | Furnace Maintenance Checklist | Filters · Pilot/ignition · Thermostat · Ductwork · Electrical connections · CO check | "Furnace Maintenance Checklist: Tips, Tricks, and More" |
| /checklists/boiler-maintenance-checklist/ | Boiler Maintenance Checklist | Pressure & temperature · Safety valves · Burner · Water treatment · Leaks · Logs | The main checklist section |

**Batch 3c: Safety / quality / compliance (fits the Safety & Quality suites)**

| Post | Template to build | Suggested sections | Place embed after |
|------|-------------------|--------------------|-------------------|
| /checklists/osha-compliance-checklist/ | OSHA Compliance Checklist | General safety · Industry-specific standards · Training & documentation · Auditing | "Key Components of an OSHA Compliance Checklist" |
| /checklists/warehouse-safety-checklist/ | Warehouse Safety Checklist | Aisles & storage · Racking · Forklifts · Docks · Fire & emergency · PPE · Housekeeping | The checklist section (the post already has 4 "get the checklist" CTAs that point nowhere) |
| /checklists/personal-protective-equipment-checklist/ | PPE Checklist (7 Types) | Head · Eye & face · Hearing · Respiratory · Hand · Body · Foot | "What Does a Personal Protective Equipment Checklist Include?" |
| /checklists/5s-audit-checklist/ | 5S Audit Checklist | Sort · Set in order · Shine · Standardize · Sustain (score each) | "The 5s audit checklist" |
| /checklists/6s-lean-audit-checklist/ | 6S Lean Audit Checklist | Sort · Set in order · Shine · Standardize · Sustain · Safety | "SAFETY" (end of the 6S list) |
| /checklists/iso-9001-audit-checklist/ | ISO 9001 Internal Audit Checklist | Scope & objectives · Documentation · Leadership · Planning · Support · Operation · Performance evaluation · Improvement | "What Should Be Included in the ISO 9001 Audit Checklist?" |
| /checklists/health-inspection-checklist-for-restaurants/ | Restaurant Health Inspection Checklist | Food storage · Temperature log · Cross-contamination · Personal hygiene · Cleaning & sanitization · Facility | "Key Areas Covered in a Health Inspection Checklist" |
| /checklists/security-audit-checklist/ | Security Audit Checklist | Access controls · Network security · Data protection · Physical security · Incident response · Training | "What Does a Security Audit Include?" |
| /checklists/how-to-create-the-best-sox-compliance-checklist/ | SOX Compliance Checklist | Internal controls documentation · Risk assessment · Access controls · Change management · Monitoring · Reporting | "Sample SOX Compliance Checklist" |
| /checklists/human-resource-compliance-checklist/ | HR Compliance Checklist | Recruiting · Onboarding & policies · Classification · DEIB · Employment law · Records | "HR Compliance Checklist" |
| /reports/how-to-write-an-incident-report/ | Incident Report Form | Scene & date/time · People involved · Description · Evidence (photos) · Root cause · Corrective actions · Sign-off | "Step 5: Review and Submit" (the H1 promises "with Templates") |

## 7. Phase 4: Priority B (optional, after Phase 3)

These are inspection guides where a template adds value, although the title doesn't promise one. Reuse a Phase 3 template wherever one fits.

| Post | Template | Reuse |
|------|----------|-------|
| /checklists/a-comprehensive-guide-to-fire-safety-inspections-in-homes-and-workplaces/ | Fire Safety Inspection | New |
| /blog/how-often-should-fire-extinguishers-be-checked/ | Monthly Fire Extinguisher Inspection | New (small, 6–8 items) |
| /checklists/best-commercial-property-inspection-of-2024-a-complete-guide/ | Commercial Building Inspection | Reuse from 3b |
| /checklists/brc-audit-checklist-requirements-and-key-insights/ | BRCGS Audit Prep | New |
| /blog/dot-out-of-service-violations-list/ | CDL Pre-Trip Inspection | Reuse from 3a |
| /blog/osha-warehouse-nep-a-peak-season-safety-inspection-guide/ | Warehouse Safety Checklist | Reuse from 3c |
| /blog/ehs-inspection-app-hazcom-deadline-audit/ | HazCom Audit Checklist | New |
| /use-case/construction-site-safety-app-winter-inspections/ | Winter Jobsite Safety Inspection | New |
| /checklists/hotel-maintenance-checklist/ | Hotel Maintenance Checklist | New. Replaces the housekeeping template set in Phase 1 |

No other posts (news, comparisons, fraud/KYPiT, financial, opinion) need a template.

## 8. Phase 5: Broken image placeholders (P1, CMS only)

For each post: replace the `[Image: ...]` paragraph with an Upload/Image block (from Media; upload it if missing) with a descriptive alt. If the image no longer adds value, delete the paragraph.

| Post | Placeholder(s) |
|------|----------------|
| /blog/construction-industry-trends-in-the-next-10-years/ | 3d-printing-house-contruction.jpg |
| /blog/difference-qms-eqms/ | 3 placeholders (paper QMS image ×2, which is a duplicate, so keep one; dashboard monitor) |
| /blog/how-often-should-fire-extinguishers-be-checked/ | Black-Minimalist-UIUX-Designer-LinkedIn-Banner (a CTA banner; replace it with a text CTA) |
| /blog/kicking-off-2025-vlx-wraps-up-2024-with-prestigious-recognitions-from-gartner-digital-markets-in2024/ | Capterra1.png, SoftwareAdvice.png, GetApp.png (award badges) |
| /blog/why-audits-failed-without-real-time-inspections/ | 2 placeholders (timestamped photos image + monitoring-risk webp) |
| /checklists/human-resource-compliance-checklist/ | Book-Demo-For-HR-Compliance banner (replace with a text CTA) |
| /checklists/rental-inspection-checklist-what-can-a-landlord-look-at-during-an-inspection/ | template_realestate.jpg (better: replace with the Phase 3 rental template embed) |
| /use-case/a-comprehensive-guide-to-the-7-multi-point-vehicle-inspection-checklist/ | MobileTemplates3.png (better: replace with the Phase 3 embed) |
| /use-case/enhance-inspection-accuracy-and-fraud-detection-with-vlxs-kypit/ | worker-validating-product-inspection-app.jpg |
| /use-case/the-strategic-advantage-of-integrated-management-system-ims-audits/ | template_safety.jpg |
| /use-case/vlx-unveils-kypit-the-new-standard-in-fraud-prevention-for-enterprise-business-validations/ | worker-inspecting-collateral-assets-at-warehouse.jpg |
| /use-case/working-offline-and-syncing-later-in-digital-inspection-apps/ | devices_a.jpg |

## 9. Supporting tasks

- **App team: add `https://dev.vlx.ai` to app.vlx.ai's `frame-ancestors`** so editors can see embeds on dev. Optional; production works without it.
- **App team: fix the food-service template description** (it describes vehicle inspections).
- **Engineering, optional:** retire or patch `scripts/migrate-wordpress.ts` so any re-run maps `app.vlx.ai` iframes to the `templateEmbed` block and images to upload blocks, instead of placeholder text.
- **Regression check after each batch:** grep the live HTML of all sitemap content URLs for `[Embedded content` and `[Image:`. The target count is 0.

## 10. Rollout & tracking

| Phase | Scope | Owner | Effort | Target |
|-------|-------|-------|--------|--------|
| 1 | Fix 5 broken embeds + 2 label fixes | CMS editor | ~1 h | Week 1 |
| 2 | Reuse 2 existing templates | CMS editor | ~20 min | Week 1 |
| 5 | 12 image placeholders | CMS editor | ~2 h | Week 1–2 |
| 3a | 6 vehicle/fleet templates | App + CMS | ~1 day | Week 2 |
| 3b | 14 home/residential templates | App + CMS | ~2–3 days | Week 3 |
| 3c | 11 safety/quality/compliance templates | App + CMS | ~2 days | Week 4 |
| 4 | 9 Priority B posts | App + CMS | ~1–2 days | Week 5 |

**Tracking sheet columns:** Post URL · Phase · Template name · Template ID · Embed placed (Y/N) · Live (Y/N) · Date.

**Batch order rationale:** 3b goes right after vehicle because it overlaps with VLX Home (home inspectors), which is the current product push. 3a comes first because those templates are short and reusable for Priority B (the DOT post).
