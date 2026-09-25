# Proposal: Financial Institutions keyword + merging Service/Inspection Companies

**Date:** 2026-09-25
**Author:** lceballos-seo
**Status:** Proposal — pending team approval (not implemented)
**Scope:** 2 decisions on service pages under `/digital-inspections-software/`

---

## Summary

This document proposes two changes and explains why they matter and how to carry them out:

1. **Change the primary keyword** of the *Financial Institutions* page to `collateral inspection software`.
2. **Merge** the *Service Companies* and *Inspection Companies* pages into one.

Both come from the same cannibalization analysis and a keyword research validated against the live SERP (market: United States). Supporting data lives in `vlx-keyword-decision-financial-y-service-companies-2026-09-24.csv` and `vlx-ranki-keyword-research-detalle-2026-09-17.csv`.

---

## Part A — Financial Institutions: change the primary keyword

### Current situation
- **Page:** `/digital-inspections-software/financial-institutions/`
- **Current keyword:** `virtual field inspection software for financial institutions`
- It describes the product precisely and we **rank #1** for it, but it has **no measurable search volume**. It is a phrase we wrote ourselves, not what buyers type. Result: a #1 spot that brings almost no traffic.

### Proposal
Adopt `collateral inspection software` as the primary and move the current one to secondary (kept in the copy because it already ranks).

### Why it matters
- **It is how the industry names the job:** banks and asset-based lending (ABL) lenders look for a tool to inspect and verify a loan's **collateral** (inventory, equipment, floor plan, construction).
- **Real demand + right intent:** the SERP is 100% loan-collateral players (First American, Alogent, Truepic…) and **vlx.ai already ranks ~5th** for the "software" variant.
- **Low competition:** KD 0, easy to climb.
- **No cannibalization:** it does not clash with the hub, asset-verification, inspection-companies or home-inspectors.
- **We lose nothing:** the current phrase stays as a secondary.

### Objective of the keyword
Capture the audience that does **not find us today** — banking/ABL teams searching for software to inspect and verify collateral — and bring them to the page with the right purchase intent.

### How to implement it
1. First reconcile the ranking data: the research says #1 for the current keyword, but GSC / the April mapping said position 9.1 (likely different queries). Verify in GSC before touching anything.
2. Unify `collateral inspection software` across **H1 + meta title + meta description + one H2 + hero alt** (same criteria as the other 19 pages).
3. Follow the `vlx-marketing` skill convention: meta title 50–60 chars before `| VLX`; meta description 129–155 with the keyword in the first 120 and a CTA at the end; H1 and ≥1 alt with the keyword up front.
4. Keep `virtual field inspection software for financial institutions` as a secondary in the copy.
5. Work **locally first** (working tree, no PR) → review → deploy in the corresponding batch.

### Open question for the team
Does the keyword **limit** the real scope the page should have? The page covers more than "collateral": field exams, portfolio monitoring, inventory and equipment verification, fraud prevention. If the business goal is broader, a more umbrella term may be warranted — though today `collateral inspection software` is the only one with demand + intent + no cannibalization. **I need to confirm the page's intended scope before locking the keyword.**

---

## Part B — Merge Service Companies + Inspection Companies

### The finding
`/service-companies/` and `/inspection-companies/` are **practically the same page**:

- **Same audience:** companies whose core business is inspections.
- **Same message:** both open with "run your inspection business on VLX".
- **Same anchor features:** tasks/work orders, branded client reports, team management, field capture.
- **Same social-proof client:** ITI International on both.
- **Already nested:** inspection-companies lists service-companies as a child card in its own "By Industry" grid.

### Why it matters
- **Internal cannibalization:** two near-identical pages compete for the same searches; Google splits the signal and neither ranks well.
- **Only one has a real keyword:** inspection-companies owns `inspection management software` (260 vol, KD 5) and is the more complete page. Service-companies **has no keyword of its own** with volume + intent + no overlap, and it **does not appear in the top 9** of its own SERP.
- Uniting the effort into a single page makes everything push in the same direction.

### How to carry it out
1. **301** in `next.config.ts`: `/digital-inspections-software/service-companies/` → `/digital-inspections-software/inspection-companies/`.
2. **Fold in** service-companies' differentiating angle (multi-client / per-client separation / white-label / free-form) into the inspection-companies copy.
3. **Remove** the service-companies child card from the "By Industry" grid.
4. Review internal links pointing to service-companies and redirect them to the unified page.
5. **Respect the deploy order:** inspection-companies deploys LAST (it is VLX's only live URL in that SERP; if it moves before the hub takes over, VLX is left with no position).

### Open question for the team
**Can they be merged**, or is there a business/product reason to keep them separate that we are not seeing? If there is none, merging is the recommendation.

---

## Risks and considerations
- All work happens **locally first**, no PR or deploy, until we have the go-ahead.
- The Financial keyword depends on confirming the **page scope** (see question A).
- The page merge is an architecture change (301) that must enter the **defined deploy order**.
- No Ranki credits available at the time of writing; the data used is from the Sep 17 and Sep 24 runs.

## Next steps (team decisions pending)
- [ ] **A.** Approve `collateral inspection software` as the Financial primary (and confirm the page scope).
- [ ] **B.** Approve the Service → Inspection Companies merge (or justify keeping them separate).
- [ ] With the OKs, implement locally and schedule the deploy in the right order.
