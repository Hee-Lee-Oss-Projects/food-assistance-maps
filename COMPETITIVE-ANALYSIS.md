# Competitive + Improvement Analysis — food-assistance-maps

Date: 2026-06-29 · Analyst: research agent · Inputs: PLAN.md v0.1.0, TASKS.md (M0–M1 skim),
web research (cited). Sibling project: `community-resource-maps`.

---

## 1. Correctness & completeness review of PLAN.md

The plan is unusually strong: it already names freshness-as-safety, HSDS, the ODbL share-alike
gate, recipient-PII-impossible-by-schema, operator-PII minimization, the "informational, not
advice" boundary, and an honest "no partner yet" posture. Findings below are refinements, plus a
few real gaps.

**Currency / accuracy — correctly central, but quantify and harden it.** The plan's spine ("a
stale entry sends a hungry person on a wasted trip") is *empirically validated* and the plan
under-cites it. A Bronx study found **only ~50% of pantries were actually open during the hours
listed in an online directory**
([PMC6330151](https://pmc.ncbi.nlm.nih.gov/articles/PMC6330151/)); an LA validation effort found
**~30% of pantries had temporarily or permanently closed since COVID**
([Food Oasis / Data Reframed](https://www.datareframed.org/news/foodoasis)); and a 12-state
analysis found **34.4% of pantries had no days-of-operation data at all**
([BMC Public Health](https://link.springer.com/article/10.1186/s12889-022-12847-0)). These numbers
should anchor §Problem and the ≥95% audit target. **Gap:** the 90/120/180-day SLA is likely *too
slow* for the most volatile facts. The Bronx 50%-open finding implies hours/operating-status for
small volunteer pantries can be wrong within weeks. Recommend a **tiered SLA by site type**
(volunteer pantry / community fridge = aggressive, e.g. 30–45 days; institutional food bank = 90+
days) rather than one global window, and a "seasonal" trigger (summer-meal and holiday-box sites
are time-boxed and must be expired automatically). The plan's volatile-vs-stable split is good;
extend it to source/site **reliability class**.

**Eligibility-info accuracy (SNAP/WIC) — boundary is right; add a verification rule.** The plan
correctly stays "informational, not advice" and defers advice to `benefits-navigator`. But it
reproduces operator/program eligibility *facts* ("must live in zip X," "ID required," "SNAP
accepted here"). SNAP/WIC rules change (income thresholds update annually; categorical eligibility
varies by state; WIC is state-administered with different vendor rules). **Gap:** there is no
explicit rule that **program-eligibility text must be sourced to the *operating authority* and
date-stamped like any other volatile fact**, and no rule against the dataset implying a *person* is
eligible. Add: eligibility statements carry their own `last_verified` + source, are quoted/
paraphrased from the authority (USDA/state agency/operator), and never resolved against an
individual. Also: SNAP-retailer "accepts SNAP" is a distinct fact-type from "free food here" —
don't blur "where you can spend benefits" with "where you can get free food."

**Privacy / dignity + non-stigmatizing design — strong on privacy, thin on dignity.** The
recipient-PII-impossible-by-schema stance is excellent and a genuine differentiator. **Gap:** the
plan treats *privacy* thoroughly but *dignity / non-stigmatizing UX* only lightly (one mention).
Dignity is a design discipline, not just a data stance — Plentiful's whole thesis is that stigma
and queues are barriers, and they engineered them out
([MOLD](https://thisismold.com/object/connected/plentifuls-reservation-app-is-designing-dignity-into-the-charitable-food-system),
[NYC Food Policy](https://www.nycfoodpolicy.org/avoiding-the-stigma-and-the-line-at-your-local-food-pantry-with-plentiful/)).
Recommend an explicit **dignity rubric** for the view: warm/non-judgmental language ("find free
groceries near you," not "for the needy"), no eligibility-shaming filters surfaced as gates,
"anyone can use a pantry" framing, no forced account, clear "no ID / no questions" flags where
true, and multilingual + low-literacy + low-bandwidth defaults. This belongs in the a11y reviewer's
remit or a new dignity-review checkpoint.

**Data standards (HSDS/OpenReferral) — correct and current.** HSDS adoption is the right call;
it is the standard adopted by Findhelp, United Way 211, and Unite Us, and endorsed by AIRS
([Open Referral docs](https://docs.openreferral.org/en/latest/hsds/overview.html)). One
correctness note: the plan's inline data model (§6) reads as a simplified single-object shape;
**HSDS 3.x is a multi-table/datapackage model** (organizations, services, locations,
service_at_location, phones, schedules, languages, taxonomy, attributes). The plan should state it
targets **HSDS 3.x Publishing format / datapackage** specifically (not a flattened approximation),
since downstream 211 import depends on exact conformance. Also pin the version (3.0 vs 3.1).

**Source-data licensing — the strongest part, with one missing competitor-source nuance.** The
ODbL share-alike treatment is correct and rare-to-see-done-right. **Critical new finding:** an
**open HSDS 3.0 food-assistance feed already exists under CC-BY-SA 4.0** at
[feedam.org/hsds](https://feedam.org/hsds) (regenerated ~nightly, REST API, attribution "Data from
Feed America"). This both (a) is a reusable *source* the plan should register, and (b) introduces a
**second share-alike vector**: CC-BY-SA is copyleft like ODbL, and **ODbL + CC-BY-SA are not
freely interoperable** in one merged database. ADR #2 currently frames share-alike as an OSM-only
problem; it must also handle **CC-BY-SA source data** and the OSM-ODbL-vs-CC-BY-SA tension.
(Caution: verify whether "Feed America"/feedam.org is the well-known Feeding America
[feedingamerica.org] or a distinct entity — the EIN and domain differ; provenance must be
confirmed before treating it as authoritative.) Separately, Feeding America's own consumer locator
([feedingamerica.org/find-your-local-foodbank](https://www.feedingamerica.org/find-your-local-foodbank))
and Findhelp ([findhelp.org](https://www.findhelp.org/)) are **proprietary** — free to *searchers*
but not openly licensed for bulk reuse; the plan's no-scraping rule correctly excludes them.

**Accessibility — well specified.** WCAG 2.2 AA + a real AT support matrix + axe/pa11y + manual
audit is above bar. Add: **low-bandwidth / feature-phone / SMS path** as an accessibility concern,
not just screen-reader support — the beneficiary population skews low-digital-access (USDA's Summer
Meals finder and Plentiful both ship SMS/text paths for exactly this reason).

**Scope / dedup vs community-resource-maps — the biggest structural question.** food-assistance-
maps is explicitly "the food-specific sibling" of `community-resource-maps`, which *also* covers
food among other human services. Both adopt HSDS, both need a verification/freshness engine, both
register the same government/OSM/HSDS sources, both will ingest overlapping records. **Risk:** two
forks of the same engine, duplicated entries, divergent licensing decisions, and confused
downstream reusers. The plan flags this (§12, Open Q8) but defers it. Recommend resolving it *now*:
extract a **shared HSDS directory + verification engine** (one schema, one provenance/freshness/
dedup/geocode pipeline, one license register), with food-assistance-maps as a **domain
configuration / taxonomy filter** over it, not a parallel codebase. See §7.

---

## 2. Competitive landscape (researched, cited)

**Feeding America locator** — the default for most US users; 200+ member food banks, zip-code
search, hours/locations. *Strengths:* authority, scale, trust, brand. *Weaknesses:* depth/freshness
varies by member food bank; **proprietary consumer locator** (not bulk-reusable); UX is
referral-to-member-bank, not a clean machine-readable national dataset for searchers.
([feedingamerica.org/find-your-local-foodbank](https://www.feedingamerica.org/find-your-local-foodbank))

**"Feed America" HSDS 3.0 open feed (feedam.org/hsds)** — the most direct competitor *and* a
reusable source. Publishes a full HSDS 3.0 directory under **CC-BY-SA 4.0**, REST API, ~nightly
regeneration. *Strengths:* already open, already HSDS, already national-scale, share-alike-friendly.
*Weaknesses:* unclear independent freshness/accuracy auditing; entity provenance needs
verification; CC-BY-SA constrains downstream relicensing. ([feedam.org/hsds](https://feedam.org/hsds))

**Findhelp (formerly Aunt Bertha)** — broad social-care search (food + housing + health), powers
many 211s and health systems. *Strengths:* breadth, referral tooling, HSDS-based, huge coverage.
*Weaknesses:* **proprietary/closed data** (free to searchers, not openly licensed); commercial SaaS;
not a public open dataset. ([findhelp.org](https://www.findhelp.org/))

**211 / United Way (e.g. 211LA)** — phone + web I&R, food among all services, HSDS/AIRS-aligned.
*Strengths:* trained specialists, 24/7, multilingual, authoritative referrals. *Weaknesses:* not
universally available; data quality varies by local 211; web datasets often not openly licensed or
machine-consumable; phone-gated. ([211la.org/food-resources](https://211la.org/food-resources))

**FoodFinder** — food-pantry-specific nonprofit locator (address/phone/hours). *Strengths:*
food-focused, simple, mission-aligned. *Weaknesses:* freshness/coverage unclear; not an open HSDS
dataset; app-centric. ([foodfinder.us](https://foodfinder.us/))

**USDA SNAP Retailer Locator** — authoritative, **open** (ArcGIS Hub, CSV export, public-domain
federal data) list of SNAP-authorized retailers. *Strengths:* authoritative, openly licensed,
downloadable. *Weaknesses:* "where to *spend* benefits," **not** free-food sites; no pantries/meals.
([fns.usda.gov/snap/retailer/data](https://www.fns.usda.gov/snap/retailer/data),
[USDA-FNS ArcGIS Hub](https://usda-snap-retailers-usda-fns.hub.arcgis.com/datasets/USDA-FNS::snap-retailer-location-data/))
WIC vendor data is **state-administered and fragmented** — no single national open finder.

**No Kid Hungry + USDA Summer Meals Site Finder** — kids' free-meal sites; **text "Summer Meals"
to 97779**, map updated **every Friday in season**. *Strengths:* authoritative, SMS path, seasonal
freshness discipline. *Weaknesses:* **kids/seasonal only**; not year-round adult food assistance.
([nokidhungry.org/free-meal-finder](https://www.nokidhungry.org/free-meal-finder),
[USDA Summer Meals Site Finder](https://www.fna.usda.gov/sfsp/sitefinder))

**Plentiful** — SMS/app **reservation** system, dignity-by-design, 9 languages, **576 NYC pantries,
1.2M users**, cut wait times from ~90 min to ~10–15. *Strengths:* the gold standard on dignity +
reduced friction; multilingual; SMS. *Weaknesses:* **NYC-centric**, reservation-not-directory,
**collects demand-side/client data** (the opposite of our zero-recipient-PII stance), closed
platform. ([plentiful.org](https://www.plentiful.org/),
[Helmsley](https://helmsleytrust.org/news-and-insights/the-plentiful-app-saving-time-for-food-pantries-and-the-people-they-serve/))

**Mutual aid / community fridge maps** — decentralized, crowdsourced (e.g. Denver Community
Fridges, NYC 120+ fridges, Community Kitchen Chicago). *Strengths:* hyper-local, 24/7 no-paperwork,
current where actively maintained, trust within community. *Weaknesses:* **scattered across
Facebook/email/Google Maps**, no standard schema, **no freshness guarantees**, frequently stale or
abandoned. ([Denver Community Fridges](https://denvercommunityfridges.com/),
[Community Kitchen Chicago](https://www.communitykitchenchicago.org/mutual-aid-map))

**Takeaway:** the space is split between **authoritative-but-narrow open data** (USDA SNAP, USDA
Summer Meals), **broad-but-proprietary** aggregators (Feeding America, Findhelp, 211),
**dignity-leading-but-closed/local** (Plentiful), and **open-but-chaotic** mutual aid. An open,
**audited-freshness, HSDS, dignity-designed, multi-source-deduped year-round** directory is an
unoccupied position.

---

## 3. Gaps we can fill

1. **Independently audited freshness** — every competitor *asserts* currency; almost none publishes
   an accuracy/freshness audit. The Bronx-50%/LA-30% evidence shows directories are routinely
   wrong. A public, quarterly-audited ≥95%-accuracy / ≥90%-within-SLA dataset is unique.
2. **One openly-licensed, HSDS, multi-source-deduped, year-round food map** — unifying pantries +
   meals + fridges + SNAP-retailer + summer-meals into a single deduped HSDS dataset (no competitor
   merges all of these cleanly, with provenance, openly).
3. **Closure as a first-class state** — competitors silently keep dead listings; explicit
   `closed`/`moved`/`temporarily_closed` + "may be out of date — call ahead" prevents wasted trips.
4. **Per-record provenance + license transparency** — proprietary aggregators can't show their
   sources; mutual-aid maps don't track them. We can.
5. **Zero recipient data, by construction** — Plentiful (and any reservation/referral system)
   collects client data; a directory that *cannot* is a privacy-first alternative.
6. **Dignity-designed open UI** — bring Plentiful-grade non-stigmatizing UX to an open, national,
   no-account directory.
7. **Mutual-aid / community-fridge sites in a standard schema** — rescue them from Facebook into
   HSDS with freshness tracking (with operator consent + PII care).
8. **SMS / low-bandwidth path** — match USDA/Plentiful's text access for low-digital-access users.

---

## 4. Differentiators to win

- **Freshness as an audited, published safety property** — the headline. Not "we try to be
  current" but "≥95% audited accuracy, ≥90% within SLA, closures demoted not hidden, every entry
  date-stamped." This is the single strongest differentiator and no open competitor offers it.
- **Open + HSDS + license-clean** — interoperable with 211/Findhelp/Unite Us, reusable by anyone,
  with honest ODbL/CC-BY-SA handling that the proprietary players can't and the mutual-aid maps
  don't.
- **Privacy + dignity together** — zero recipient PII *by schema* plus Plentiful-grade
  non-stigmatizing design, in an open no-account product.
- **Multi-source corroboration with provenance** — authoritative-source-or-≥2-signals beats both
  single-source government feeds and unverified crowdsourcing.
- **Year-round, all-types, deduped** — pantries + meals + fridges + summer-meals + SNAP-retailer in
  one map, where rivals are siloed by type or season.

---

## 5. Claude API leverage — and the hard limits

**Where Claude helps (extraction/triage/structuring, never authority):**
1. **HSDS normalization at ingest** — parse messy operator pages / PDFs / CSVs / OSM tags into the
   HSDS multi-table model (organization/service/location/schedule/eligibility/languages), flagging
   low-confidence fields. High leverage; this is the bulk of pipeline toil.
2. **Currency-triage / staleness detection** — diff a re-fetched source against the stored record,
   detect "hours changed / site now says permanently closed / phone disconnected," and **raise a
   re-verify flag for a human** — never auto-stamp `last_verified`.
3. **Eligibility-info structuring + plain-language** — turn an operator's prose ("must reside in
   90011, photo ID, once/month") into structured `eligibility[]` and a plain, multilingual,
   low-literacy summary — **quoting/citing the operator/authority**, not inferring rules.
4. **Dedup/reconciliation assist** — cluster likely-duplicate records across sources and *propose*
   merges with a precedence rationale, for human/precedence-rule confirmation.
5. **Dignity/language QA** — flag stigmatizing or judgmental phrasing in generated summaries against
   the dignity rubric.
6. **Translation drafts** for the multilingual view (human-reviewed for high-stakes eligibility
   text).

**Where Claude must NOT decide (hard rules, several already in PLAN — keep them):**
- **Currency/operating-status: human-verified.** Claude may detect and flag staleness; only a human
  (`phone-confirmed`/`operator-confirmed`/`partner-confirmed`) sets a verified status or
  `last_verified`. The plan's "AI may not self-certify" rule is correct and load-bearing.
- **Eligibility rules: accurate + sourced, never AI-asserted.** Claude restructures/paraphrases
  operator/authority text with citation and date; it must **not** invent thresholds, decide who
  qualifies, or generalize a rule across jurisdictions.
- **No fabricated resources.** A site Claude cannot tie to ≥1 authoritative source or ≥2 signals
  stays in the unverified pool — never published. (Defends against the source-poisoning threat in
  §14.)
- **Licenses: human-verified.** Claude may *read* a terms page and *propose* a license class; the
  licensing reviewer confirms before ingestion. CC-BY-SA / ODbL share-alike resolution is a human
  ADR decision.
- **Privacy/dignity/operator-PII:** the opt-out / approximate-area / "is this a private home"
  judgment is a human privacy-review call, not an LLM classification gate alone.

(Per CLAUDE.md: the donated lane never runs the agent headless; Claude-in-the-loop here means
extraction/flagging under human verification. Any large batch ingestion uses the escrow-capped
`packages/runner` funded sub-lane with a hard per-task budget cap.)

---

## 6. Ten concrete optimizations

1. **Tier the freshness SLA by site type** (volunteer pantry/fridge ~30–45d; institutional bank
   ~90d) and auto-expire **seasonal/time-boxed** sites (summer meals, holiday boxes) — the Bronx
   50%-open finding shows one global 90-day window is too slow for volatile sites.
2. **Register feedam.org/hsds as a source + extend ADR #2 to CC-BY-SA** — there is already an open
   HSDS food feed; decide ingest-or-not, and handle the ODbL-vs-CC-BY-SA copyleft interaction (they
   don't freely co-mingle). Verify the entity's provenance first.
3. **Add a dignity rubric + dignity-review checkpoint** — non-stigmatizing language, "anyone can
   use this," no-ID/no-questions flags, no eligibility-shaming gates; review it like a11y.
4. **Ship an SMS / low-bandwidth path** (or a text-export/short-URL) for low-digital-access users,
   matching USDA Summer Meals (97779) and Plentiful.
5. **Pin HSDS to a multi-table 3.x datapackage** target (not a flattened object) and add a 211
   round-trip conformance test, so a real 211/Findhelp can import without glue.
6. **Eligibility fields get their own `last_verified` + authority source + "informational, not
   advice" label**, and never resolve eligibility for an individual; separate "accepts SNAP/WIC"
   (spend) from "free food" (get) as distinct service types.
7. **Add a "report a problem / this was closed" lightweight signal** (no account, no PII) that feeds
   the re-verify queue — turns wasted-trip harm into a freshness input. Treat as a *lead*, gated by
   the same human verification (anti-poisoning).
8. **Publish the freshness + accuracy audit publicly** (a "data confidence" page) — make the audited
   currency a visible product feature competitors lack.
9. **Bootstrap from authoritative open sources first** — USDA SNAP Retailer (PD), USDA Summer Meals,
   state/county open portals — to seed coverage fast and license-clean before harder OSM/mutual-aid
   merges; record geocode confidence from US Census Geocoder (PD).
10. **Extract the shared HSDS engine with `community-resource-maps` now** (one schema/pipeline/
    license register; food = taxonomy config), preventing a fork, duplicate records, and divergent
    license calls.

---

## 7. Parallel & perpendicular spin-offs

- **Shared HSDS directory + verification engine (parallel, foundational).** The single highest-
  leverage move: one open-source engine — HSDS schema + provenance/freshness/dedup/geocode pipeline
  + license register + accessible directory view — that **`community-resource-maps`** and
  **food-assistance-maps** both consume, each as a domain/taxonomy configuration. Resolves Open Q8
  and the §1 dedup risk; every other Elyos directory (housing, health, legal-aid) reuses it.
- **`benefits-navigator` (perpendicular, high-risk sibling).** food-assistance-maps stays
  informational and **links out** to it for SNAP/WIC *eligibility advice + application help* — the
  clean risk-tier boundary. The directory feeds it "where to apply / where benefits are accepted."
- **`open-gardening-guides` / `food-security-briefs` (perpendicular).** A "food security" content
  bundle: the map answers *where to get food today*; gardening guides answer *grow your own*;
  food-security-briefs provide the regional data/analysis. Cross-link as a portfolio.
- **MCP server (perpendicular, distribution).** Expose the HSDS dataset as an **MCP server** so any
  Claude-based assistant / 211 chatbot / civic app can query "open food assistance near zip X
  tonight, verified within SLA" with provenance and a "call ahead / last verified" caveat. Turns the
  open dataset into agent-native infrastructure and is the cleanest reuse channel.

---

## 8. Open questions

1. **Is "Feed America" (feedam.org) the same entity as Feeding America (feedingamerica.org)?** Their
   open CC-BY-SA HSDS feed changes the build-vs-reuse calculus — verify provenance before ingest.
2. **Build-vs-augment:** if a credible open national HSDS feed already exists, is our differentiator
   *audited freshness + dignity UI on top of it* rather than re-ingesting from scratch? That could
   sharpen scope dramatically.
3. **Shared engine vs. separate** with `community-resource-maps` — decide before either ingests
   (currently deferred; recommend deciding now).
4. **SLA tiers:** what are the right per-site-type windows, and who owns seasonal auto-expiry?
5. **CC-BY-SA + ODbL co-mingling:** can a single published dataset legally include both, or do we
   need the dual-track / separate-bundle approach from ADR #2?
6. **SMS/low-bandwidth path:** in scope for v1 or backlog? It materially affects the beneficiary
   most in need.
7. **Partner + priority region** (the binding human dependency) — and who performs last-mile phone/
   visit verification at the volatile-site cadence the data actually needs.
8. **Operator consent for mutual-aid/fridge sites** — many are informal/home-adjacent; opt-in +
   approximate-area policy before listing.

---

### Sources
- https://pmc.ncbi.nlm.nih.gov/articles/PMC6330151/ (Bronx pantry accuracy study)
- https://www.datareframed.org/news/foodoasis (LA ~30% closed validation)
- https://link.springer.com/article/10.1186/s12889-022-12847-0 (12-state pantry hours analysis)
- https://feedam.org/hsds (open HSDS 3.0 CC-BY-SA food feed)
- https://docs.openreferral.org/en/latest/hsds/overview.html (HSDS / Open Referral)
- https://www.feedingamerica.org/find-your-local-foodbank (Feeding America locator)
- https://www.findhelp.org/ (Findhelp)
- https://211la.org/food-resources (211)
- https://foodfinder.us/ (FoodFinder)
- https://www.fns.usda.gov/snap/retailer/data ; https://usda-snap-retailers-usda-fns.hub.arcgis.com/datasets/USDA-FNS::snap-retailer-location-data/ (USDA SNAP, open)
- https://www.nokidhungry.org/free-meal-finder ; https://www.fna.usda.gov/sfsp/sitefinder (free/summer meals)
- https://www.plentiful.org/ ; https://thisismold.com/object/connected/plentifuls-reservation-app-is-designing-dignity-into-the-charitable-food-system ; https://helmsleytrust.org/news-and-insights/the-plentiful-app-saving-time-for-food-pantries-and-the-people-they-serve/ (Plentiful, dignity)
- https://denvercommunityfridges.com/ ; https://www.communitykitchenchicago.org/mutual-aid-map (mutual aid / fridges)
