# PLAN — food-assistance-maps (open, freshness-tracked directories of food assistance)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated
> (with an optional funded sub-lane for large, capped batch ingestion — see §6, §12)

## Executive summary

**food-assistance-maps** is an open, **freshness-tracked directory dataset** of food banks, food
pantries, community fridges, soup kitchens / hot-meal programs, school and summer meal sites, and
related food-assistance services, organized by region and published as open data plus a thin,
accessible directory/map view. The core deliverable is **data, not an app**: a clean, interoperable,
provenance-rich dataset that anyone — 211 services, mutual-aid groups, civic apps, map makers —
can reuse to help food-insecure people find a place to get food **today**.

The defining property of this project is **freshness as a safety property**. A food-assistance
directory is not a neutral list: when a food-insecure person — often without spare money for
transport, sometimes with children in tow — travels to a pantry that has closed, moved, changed its
hours, or never existed, the cost of that error is real, concrete harm. We therefore treat a stale or
unverified entry the way the disaster project treats mis-stated safety advice: **no entry is
published as authoritative without verified, recently-checked contact details, recorded provenance,
and an explicit "last verified" date**, and every entry carries a re-verification SLA.

The project adopts the **Open Referral Human Services Data Specification (HSDS)** as its canonical
schema so the data is interoperable with the wider community-resource ecosystem (211/AIRS, civic
tech) rather than a bespoke silo. Code is **MIT**; the **data license is decided deliberately per
source** because the most useful open source — OpenStreetMap — is **ODbL share-alike** (copyleft for
databases), which constrains the output license of any OSM-derived database (see §7, the headline
gate alongside freshness).

This is a **medium risk-tier** project. It is **not** a high-stakes legal/medical advisory: it
locates services and reproduces operator-published facts. Where it brushes against benefits/eligibility
("who can use SNAP / a given pantry"), it stays strictly **informational, not advice**, reproduces
program operators' own published criteria with citation, and is **non-partisan** — it does not
editorialize about food, poverty, or government-assistance policy.

**Honesty note on the partner:** there is **no named partner organization, food-bank network, 211/AIRS
member, or mutual-aid group, and no MOU or verified-need confirmation on file yet.** Until one is
secured, the partner and verified-need status are **TO BE SECURED** (`verifiedNeed = false`,
`requestor = "TBD"`), and the project ships only as a generic public good. See *Problem &
beneficiaries* and *Open questions*.

## Problem & beneficiaries

**The problem.** Information about where to get free food is **fragmented, inconsistent, and rots
fast.** It is scattered across individual food-bank locators, church bulletins, PDF flyers, 211
phone lines, Facebook posts, and out-of-date third-party lists. Hours, eligibility rules ("must live
in zip code X," "ID required," "once per month"), languages served, accessibility, and even whether a
site still operates change frequently — especially for small, volunteer-run pantries and community
fridges. The people who most need this information are the least able to absorb the cost of it being
wrong: a wasted bus fare, a missed work shift, hungry children, and the discouragement of a fruitless
trip. Meanwhile, the data that *does* exist is often locked in proprietary locators or
unclear-license aggregations that downstream helpers cannot legally or practically reuse.

**Who is helped (beneficiaries).**
- **Food-insecure individuals and households** — including people experiencing homelessness, the
  newly unemployed, low-income families, students, seniors on fixed incomes, and people with limited
  English proficiency or limited digital access — who need to find an **open, correct, reachable**
  food resource near them, with accurate hours, eligibility, and access details.
- **Frontline helpers and intermediaries** — 211/AIRS information-and-referral specialists, social
  workers, school liaisons, clinic staff, librarians, shelter and mutual-aid volunteers — who make
  referrals and need trustworthy, current data they can stand behind.
- **Civic-tech and map reusers** — anyone building a finder app, a regional map, or a chatbot who
  needs an openly-licensed, interoperable (HSDS) base dataset instead of re-scraping the web.
- **The food-assistance providers themselves** — small pantries gain accurate visibility without
  building or buying their own locator.

**Verified need / partner org: TO BE SECURED.** The *general* need is well-documented by public food-
security statistics and the visible fragmentation of existing listings, so foundation work (M0–M1) is
justified. But Elyos's "delivered, not merged" bar requires a **named beneficiary or partner**: we
will treat the need as **plausible but unverified** until at least one named partner — a food-bank
network, a 211/AIRS center, a community-action agency, or an established mutual-aid organization —
confirms it in writing (letter of support or lightweight MOU), identifies a **priority region**, and
ideally commits to help with **last-mile verification** (the human step AI cannot do). Partner-specific
endorsement is required before the project's *Definition of Shipped* is met.

## Goals and non-goals

**Goals.**
- Publish an **openly-licensed, HSDS-conformant** dataset of food-assistance services for at least one
  region, with every published entry **provenance-stamped and freshness-tracked**.
- Make **freshness a first-class, enforced property**: every entry has a `last_verified` date, a
  verification method, a source trail, and a re-verification SLA; stale entries are flagged or
  withheld, never silently presented as current.
- Provide a **thin, accessible (WCAG 2.2 AA) directory/map view** and machine-readable exports
  (HSDS JSON/CSV, GeoJSON) so both people and downstream apps can use it.
- Be rigorously **license-clean**: only open / public-domain / CC / explicitly-permitted sources, with
  per-source license recorded and the ODbL share-alike implications handled correctly.
- Protect privacy: **never** collect or publish any data about food-assistance *recipients/clients*,
  and minimize incidental **operator PII** (e.g. a home address/personal phone of a volunteer-run
  pantry).
- Stay **informational and non-partisan**: reproduce operators' own published eligibility/program
  facts with citation; no eligibility *advice*, no policy editorializing.
- Be **adoptable** by a partner org and **reusable** by the wider ecosystem.

**Non-goals.**
- **Not** a real-time inventory/stock tracker ("does this pantry have eggs right now").
- **Not** a benefits-eligibility *advisor* or application tool, and **not** legal/immigration/medical
  advice (link out to official program sources; that is the separate `benefits-navigator` project's
  high-risk territory).
- **Not** a collector of any recipient/client personal data; no user accounts, no sign-in to "claim"
  aid, no demand-side data.
- **Not** a crowd-sourced free-for-all where anyone can publish an unverified location without the
  provenance + freshness gate.
- **Not** a fundraising, donation-solicitation, or commercial lead-gen platform; **not** a re-skin of,
  or competitor lock-in for, any single proprietary locator.
- **Not** a global "boil the ocean" build at launch — coverage scales region by region, verified
  before breadth.

## Success metrics (outcomes)

Outcome-centric and beneficiary-first. Baselines are zero at project start unless noted. Because we
collect **no recipient telemetry**, downstream-reach numbers come from partner self-report and
referral logs, not in-app surveillance of vulnerable users.

| Outcome | Baseline | Target (first 12 months post-launch) | How measured (privacy-preserving) |
| --- | --- | --- | --- |
| Partner orgs endorsing/adopting the dataset | 0 | ≥ 1 named partner (food-bank network / 211 / mutual aid) adopts; goal 3 | Signed letters of support / MOUs (manual record) |
| Regions with a published, freshness-tracked directory | 0 | ≥ 1 region fully published; goal 3 | Repo dataset manifest |
| **Entry accuracy on independent spot-check audit** | n/a | ≥ 95% of audited published entries are open/reachable and have correct core facts (existence, location, hours, eligibility) | Quarterly random-sample re-verification audit |
| **Entry freshness (within SLA)** | n/a | ≥ 90% of published entries `last_verified` within the SLA window at any time | Automated freshness report in CI |
| Entries with complete provenance + ≥2 independent signals (or 1 authoritative source) | n/a | 100% of published entries | Build-time validation |
| Accessibility conformance (directory/map view) | none | WCAG 2.2 AA verified; 0 critical axe violations; manual AT pass | axe/pa11y + manual screen-reader audit |
| Open reuse / interoperability | 0 | ≥ 2 downstream reusers or integrations (e.g. a 211 import, a community map) | Reuser register / inbound reports |
| People helped to a correct resource (partner-reported, opt-in) | 0 | ≥ 2,000 confirmed successful referrals/look-ups attributable to the dataset over 12 months | Partner self-report via standard template (no client tracking) |
| Privacy posture | n/a | 0 recipient/client PII fields; operator-PII minimized + documented; 0 third-party trackers | Static + runtime audit in CI; PII review log |

We deliberately avoid vanity metrics (raw entry count, page views, stars). **A large directory that
is stale or wrong is a liability, not an outcome** — accuracy and freshness are weighted above
breadth, and a smaller verified directory beats a larger unverified one.

**Reach measurement — windows, denominators, anti-double-counting.** "People helped" counts
**distinct successful look-ups/referrals attributable to the dataset** over a **rolling 12-month
window** from first publication. Each partner counts a person **once per reporting period** across
touchpoints; people helped via more than one partner are de-duplicated by the steward at roll-up
(best-effort, partner-attested). Estimates are recorded **as estimates**, separately from confirmed
counts. No personally identifying data about people helped is collected to produce this number.

## Scope

**In scope.**
- A canonical **HSDS-conformant data model** for organizations, services, locations, schedules,
  eligibility, accessibility, and languages, **extended** with provenance + freshness fields.
- **Ingestion** from license-cleared open sources (government open data, OSM, Open Referral/HSDS feeds,
  partner-provided lists, operators' own published pages) with per-source license capture.
- A **verification + freshness pipeline**: multi-source corroboration, geocoding (open geocoders),
  de-duplication, closure handling, `last_verified` stamping, staleness flagging, and a
  re-verification cadence.
- **Open exports**: HSDS Publishing format (JSON/CSV), GeoJSON for mapping, and a documented schema.
- A **thin, accessible directory/map view** (search/browse by location, filter by service type,
  hours, eligibility, language, accessibility) — a consumer of the dataset, kept deliberately minimal.
- A **provenance + verification log** and a **license register** that the Definition of Shipped checks
  against.

**Out of scope (explicit).**
- Real-time stock/inventory, queue lengths, or live "open now" sensing beyond published schedules.
- Benefits/eligibility **advice**, application assistance, immigration/legal/medical guidance
  (link out only; defer to `benefits-navigator` for the high-risk advisory lane).
- **Any** recipient/client personal data, sign-ups, "reserve a slot," or demand-side accounts.
- Unverified crowd submissions published without the provenance + freshness gate.
- Scraping or ingesting sources whose license/terms we cannot clear (no proprietary locator scraping).
- Donation processing, fundraising, ad/affiliate monetization.
- Authoritative coverage for regions we cannot source-verify.

## Solution approach & architecture

**Overview.** A **data pipeline + open dataset** with a thin static front end. The product of record is
the dataset and its provenance/freshness metadata; the UI is a reference consumer. No PII backend
exists because no recipient data is ever collected.

**Pipeline stages (each stage is auditable and re-runnable).**
1. **Source registry & license gate.** Every source is registered first (name, URL, license/terms,
   attribution requirement, share-alike?, retrieval method, PII risk). A source that fails the license
   gate is not ingested. This register is the front door of the whole pipeline.
2. **Ingest / extract.** Pull or parse the source into a normalized staging form (no raw verbatim
   copyrighted prose; structured facts only — name, address, hours, services, eligibility, contact).
3. **Normalize & geocode.** Map to the HSDS model; geocode addresses with **open geocoders only**
   (e.g. OSM/Nominatim, Pelias, US Census Geocoder), recording geocode source + confidence; never a
   provider whose terms forbid storing results.
4. **De-duplicate & reconcile.** Cluster likely-duplicate entries across sources; merge with a
   precedence rule (authoritative/operator source wins); keep the full source trail.
5. **Verify & stamp freshness.** Apply the verification policy (below): require multi-source
   corroboration or one authoritative source; set `verification_method`, `verified_by`,
   `last_verified`, `confidence`, and the re-verification due date. Entries that don't clear the gate
   stay in a staging/"unverified" pool and are **not published as authoritative**.
6. **Validate.** Build-time JSON-Schema validation (HSDS + Elyos provenance/freshness extension);
   reject entries missing required citation/verification/license fields; reject recipient-PII fields.
7. **Publish.** Emit HSDS JSON/CSV + GeoJSON + the directory/map view, plus a public freshness report
   and provenance log.
8. **Re-verify (loop).** Scheduled staleness sweep flips overdue entries to "needs re-verification"
   and surfaces them to humans/partners.

**Verification policy (the safety core).** An entry is publishable as **authoritative** only if it has
**either** (a) **one authoritative source** — the operator's own current published page, a partner-
confirmed record, or an official government dataset — **or** (b) **≥2 independent corroborating
signals**. The **strongest verification methods require a human** (a partner or volunteer confirming
by phone/visit/operator contact); AI sessions perform extraction, cross-source corroboration,
consistency checks, and staleness detection, and **flag** entries for human confirmation rather than
fabricating a "verified" status. Closure is first-class: an entry can be marked
`closed` / `temporarily_closed` / `moved`, and a contradicted-but-unconfirmed entry is **demoted**
(shown with a "may be out of date — call ahead" caveat or withheld), **never silently kept**.

**Freshness SLA (recorded in an ADR; indicative defaults).** Volatile fields (hours, eligibility,
operating status) re-verified at least every **90 days**; entries **flagged stale at 120 days** and
**demoted/withheld from "authoritative" at 180 days** until re-verified. Stable fields (name,
location) on a longer cadence. SLA windows are configurable per source reliability.

**Components.**
- **Schema & validation:** TypeScript/ESM types generated from the HSDS + provenance/freshness JSON
  Schema; an `ajv`-based validator in the build/CI.
- **Ingestion adapters:** one small, isolated adapter per source (vendor-/source-specific logic lives
  in adapters, per Elyos architecture rules; the core model stays source-neutral).
- **Pipeline core:** normalize → geocode → dedupe → verify → validate → publish, each idempotent and
  logged.
- **Directory/map view:** static, accessible TS front end consuming the published GeoJSON/HSDS exports
  (lightweight a11y-friendly framework, decided by ADR; map tiles from an openly-licensed provider
  with attribution).
- **Exports & API:** static HSDS JSON/CSV + GeoJSON files (a read-only static "API"); optional
  HSDA-style endpoint is backlog.
- **Build/CI:** pnpm workspace; lint, typecheck, unit, schema-validation, freshness report,
  a11y, and a **no-recipient-PII / no-tracker** audit.

**Data model (entry, HSDS-aligned + Elyos extension).**
```
ServiceAtLocation {                 // HSDS core (subset)
  organization: { id, name, url, description }
  service:      { id, name, status, eligibility[], languages[], fees }   // status: active|temporarily_closed|inactive
  location:     { id, name, address, latitude, longitude, accessibility[] }
  schedule:     [ { opens_at, closes_at, byday, valid_from, valid_to } ] // hours / availability
  service_area: [ region/zip constraints ]                                // e.g. "residents of zip X"
  contact:      { phone?, email?, public_only: true }                     // public org contacts only

  // --- Elyos provenance + freshness extension (required to publish) ---
  sources:            Citation[]   // { name, url, license, retrievedDate, attribution, shareAlike }
  verification_method: "official-source" | "operator-confirmed" | "partner-confirmed"
                       | "phone-confirmed" | "multi-source-corroborated" | "unverified"
  verified_by:        string       // human/partner attribution (not "AI")
  last_verified:      date
  reverify_due:       date         // last_verified + SLA window
  confidence:         "high" | "medium" | "low"
  operating_status:   "open" | "temporarily_closed" | "moved" | "closed" | "unconfirmed"
  pii_review:         "clear" | "operator-pii-minimized"   // recipient PII forbidden by schema
  data_license:       string       // resolved per source share-alike rules (see §7)
  reviewLogRef:       string       // PR-tied verification-log entry
}
```
Recipient/client fields are **not representable** in the schema (no name/DOB/benefits/household
fields), so client PII cannot be stored even by mistake.

**Key decisions (recorded as ADRs in M0).**
1. **Adopt HSDS** (Open Referral) as the canonical schema + the provenance/freshness extension shape.
2. **Data-license resolution** for share-alike (ODbL/OSM) vs. permissive sources — the project's
   headline licensing decision (see §7).
3. **Freshness SLA** windows + staleness state machine (flag/demote/withhold) + closure handling.
4. **Verification policy** thresholds (authoritative-source vs. ≥2-signal; human-required methods).
5. **Geocoder** choice (open-only) + map-tile provider + attribution.
6. **UI framework / hosting** (static) for the thin directory/map view.

**Decision ordering.** The **HSDS adoption ADR (#1) and data-license ADR (#2) are decided before** the
schema is finalized and before any region is ingested — both are inputs to what we can legally store
and publish. Until #1/#2 land, the schema above is **provisional**, and TASKS.md sequences ingestion
behind them.

## Data, licensing & compliance

**This section is load-bearing for a medium-risk, license-sensitive data project. Be conservative.**

**Data sources (each registered + license-checked before ingestion).**
- **Government open data** — federal/state/county/municipal open-data portals, USDA program site
  listings, school/summer-meal site lists. US federal works are generally **public domain**; state/
  local portals vary (many CC0/CC-BY/OGL) — **verify per portal**, do not assume.
- **OpenStreetMap** (`amenity=social_facility` + `social_facility=food_bank`, community fridges, soup
  kitchens) — **licensed ODbL** (Open Database License): **attribution + share-alike** for databases.
- **Open Referral / HSDS feeds** — many 211/AIRS and community datasets publish HSDS; **license varies
  per publisher** (some open, some restricted) — verify and record each.
- **Operators' own published pages** — a food bank's own website/hours are facts published by the
  operator; we reproduce **facts with citation**, not verbatim copyrighted prose, and respect
  robots/terms.
- **Partner-provided lists** — once a partner is secured, their shared data under agreed terms.

**Licensing rigor (critical) — the ODbL share-alike gate.**
- **Our code:** **MIT**.
- **Our data:** there is a genuine tension that must be resolved by ADR #2, not glossed over.
  OpenStreetMap data is **ODbL**, a **share-alike copyleft for databases**: a "Derivative Database"
  built from OSM must itself be offered under **ODbL**, and a "Collective Database" must at minimum
  attribute OSM. A naive "everything CC-BY-4.0" claim over an OSM-derived directory would be a
  **license violation.** The resolution options (decided in ADR #2, recorded in the license register):
  1. **License the OSM-derived database as ODbL-1.0** (attribution + share-alike) — simplest, fully
     compliant; downstream reusers inherit share-alike.
  2. **Keep OSM as a verification/lead signal only** (not merged into the published database) and build
     the published database from permissive/PD sources + original verification, licensed **CC-BY-4.0**.
  3. **Dual-track**: an ODbL "full" dataset including OSM-derived records, and a separate CC-BY subset
     containing only records sourced from permissive/PD/original-verification data.
  The default until ADR #2 is decided is **conservative**: treat any OSM-derived records as ODbL and
  **do not** relabel them CC-BY. Every record carries its resolved `data_license`, and the export
  bundle states the effective license(s) and required attributions.
- **Per-record provenance** is mandatory: `sources[]` with name, URL, license, retrieval date,
  attribution string, and a `shareAlike` flag drives the license-resolution logic above.
- **No verbatim copying** of copyrighted descriptions; we store **structured facts** (addresses,
  hours, eligibility statements) which are far less protectable, with attribution to the operator.

**Provenance model.** Every published entry carries its full `sources[]`, `verification_method`,
`verified_by`, `last_verified`, and a **PR-tied verification-log entry** (append-only, in-repo, bound
to a commit). The license register + provenance log are the auditable records the *Definition of
Shipped* checks against.

**Privacy / PII stance.**
- **Recipients/clients: zero data, ever.** No accounts, no "who used aid," no demand-side data; the
  schema cannot represent it. This is the strongest privacy line in the project.
- **Operator PII: minimized and reviewed.** Many small pantries and community fridges are run by
  individuals from **home addresses** or **personal phone numbers**. We publish **public,
  organization-level** contact info; where a listing's address/phone is plausibly a private
  individual's, we (a) prefer the operator's published public channel, (b) offer an **opt-out / "do
  not list exact address"** path (show approximate area + contact instead), and (c) record a
  `pii_review` decision. A PII-review step is part of verification, not an afterthought.
- **No telemetry / trackers** in the directory view; reach is measured by partner self-report, not by
  watching vulnerable users.

**Non-partisan & "not advice" stance.** Food assistance and public benefits are politically charged.
The dataset and UI **reproduce operators' and programs' own published facts with citation** and add
**no political framing, no commentary on policy, no eligibility advice.** Program/eligibility text is
sourced to the operating authority and labelled **"informational, not legal or benefits advice — check
with the provider."** This keeps the project within its medium-risk tier and out of the high-risk
advisory lane.

**Attribution.** OSM ("© OpenStreetMap contributors, ODbL"), each government portal, each operator,
and map-tile providers are credited in the export bundle and an in-app credits/sources page. We do
**not** imply endorsement by any source or agency we cite.

## Quality, review & risk gates

**Risk tier: medium.** Domain-accuracy + freshness review is mandatory; sending a food-insecure person
to a wrong/closed location is real harm. (This project is **not** high-risk: it does not give
legal/medical/benefits advice. If a future module crossed into eligibility *advice*, that module would
be split out at **high** risk with a licensed-professional review gate and an explicit "informational,
not legal advice" frame — see *Open questions*.)

**Required reviews before a deed is "done":**
- **Code/pipeline/UI tasks:** maintainer code review + CI green (lint, typecheck, unit,
  **schema-validation**, **freshness report**, **a11y**, **no-recipient-PII/no-tracker** audit).
- **Data/entry tasks (medium risk) — the freshness & provenance gate:**
  - Every published entry has **complete provenance** (`sources[]` with license) and clears the
    **verification policy** (one authoritative source **or** ≥2 independent signals).
  - Every published entry has a **`last_verified` within the SLA** and a `verification_method` /
    `verified_by`; **AI may not self-certify "phone-confirmed"/"operator-confirmed"** — human-required
    methods need a human attribution.
  - **License resolution** is correct per source (ODbL share-alike honored); the `data_license` field
    matches the source rules.
  - **PII review** is recorded (recipient PII absent by schema; operator-PII minimized).
  - **No self-approval:** the contributor who ingested/verified an entry and the **reviewer** who
    approves it are **distinct**; the approving reviewer for a region should have **local knowledge**
    or partner standing where possible. Each approval writes a **PR-tied verification-log entry**
    (PR #, commit SHA, entries covered, sources/licenses checked, dedup + closure decisions).
- **Accessibility:** every UI-affecting change passes automated checks **and** a **manual
  assistive-technology audit** against a defined support matrix (NVDA+Firefox/Win, JAWS+Chrome/Win,
  VoiceOver+Safari/macOS·iOS, TalkBack+Chrome/Android, plus keyboard-only per desktop browser), before
  each milestone exit and each release, signed off by the accessibility reviewer.
- **Audit:** a **quarterly random-sample re-verification audit** (independent of the ingesting
  contributor) measures real-world accuracy against the ≥95% target and feeds corrections back.

**Definition of Shipped (project-level).** A deployed, openly-licensed, **HSDS-conformant** directory
that: (1) publishes only entries that clear the **provenance + verification + freshness gate**,
(2) is **license-clean** with correct per-source licensing (ODbL share-alike honored) and a complete
license register, (3) contains **no recipient PII** and minimized, reviewed operator PII, (4) provides
an **accessible** directory/map view (WCAG 2.2 AA, automated + manual) and machine-readable exports,
(5) sustains **≥90% freshness within SLA** and **≥95% audit accuracy** for at least one region, and
(6) is **adopted/endorsed by a named partner** and/or demonstrably reused downstream. Until a partner
is secured, criterion (6) is **outstanding** and the project is "publicly usable" but not yet
"shipped" by Elyos's *delivered, not merged* bar.

**"Publicly shipped (no partner)" success state — so a finished dataset isn't stranded.** Criteria
(1)–(5) can be fully met without any partner. If, by a **decision point set at 6 months after the M3
build is production-ready**, no partner has been secured, the steward + Elyos governance may declare
**"Publicly Shipped (generic public good)"**: the dataset + view are deployed, announced, and offered
to mutual-aid/civic channels, with accuracy/freshness maintained by best-effort community
re-verification. A later partner endorsement **upgrades** the status rather than re-opening launch.

## Roadmap & milestones

Phased; each phase has measurable exit criteria. M0 is a thin cold-start foundation.

- **M0 — Foundation & cold-start (thin slice).**
  Goal: the schema, license/provenance/freshness model, and one tiny verified pilot slice.
  Exit criteria: **HSDS-adoption ADR (#1) and data-license/share-alike ADR (#2) recorded before**
  the schema is finalized; HSDS + provenance/freshness JSON Schema published with build-time
  validation; source registry + license register established with ≥3 sources cleared; freshness SLA +
  verification-policy ADRs recorded; **a small pilot set (~25–50 entries in one locality) ingested,
  geocoded, deduped, verified to policy, and published** with full provenance and `last_verified`;
  CI runs lint/typecheck/unit/schema-validation/freshness-report and a no-recipient-PII/no-tracker
  audit; a minimal directory view renders the pilot.

- **M1 — Verification & freshness pipeline (the safety core).**
  Goal: turn the one-off pilot into a repeatable, enforced verification + freshness system.
  Exit criteria: ingestion adapters for ≥3 source types; dedup/reconciliation with precedence;
  open-geocoder integration with confidence; **staleness state machine** (flag/demote/withhold) and
  closure handling working; freshness report shows ≥90% within SLA for the pilot region; verification
  log enforced in CI (no publish without provenance + method + reviewer-distinct-from-author);
  operator-PII review step operational.

- **M2 — Coverage, accessibility & open distribution.**
  Goal: a complete, accessible region + reusable open exports.
  Exit criteria: ≥1 region **fully** covered and audited at ≥95% accuracy; directory/map view meets
  WCAG 2.2 AA (automated + manual AT audit); HSDS JSON/CSV + GeoJSON exports published with a documented
  schema and effective-license/attribution statement; ODbL share-alike correctly applied to any
  OSM-derived export; at least one search/filter (location, service type, hours, language, accessibility).

- **M3 — Partner adoption, audit & sustainability.**
  Goal: secure a named partner, prove real-world accuracy, and stand up ongoing maintenance.
  Exit criteria: ≥1 **named partner** endorsement/adoption on file (`verifiedNeed = true`); quarterly
  re-verification audit operational and meeting ≥95% accuracy + ≥90% freshness; outcomes-tracking
  process (partner self-report) in place; documented re-verification cadence + steward ownership;
  ≥3 regions or ≥2 downstream reusers as a stretch.

Dependencies: M1 depends on M0 (schema + ADRs + pilot). M2 depends on M1 (working verification/
freshness pipeline). M3 depends on M2 (a complete, exported region) and on the **partner being
secured** (pursued in parallel from M0).

## Work breakdown

The itemized, schema-mapped backlog lives in **TASKS.md**, organized by the M0–M3 milestones above.
Each task maps to an Elyos Task JSON (per `packages/schema`), is sized (small/medium/large),
risk-tagged, and names a reviewer. TASKS.md also includes acceptance criteria for the most important
tasks per milestone, milestone Definitions of Done, a backlog, and a complete example Task JSON
(`verifiedNeed = false`, `requestor = "TBD"` until a partner is secured).

## Governance, roles & stakeholders

- **Maintainer (Owner): TBD.** Owns the repo, schema, roadmap, releases, and review standards.
- **Code reviewers:** rotation of TS-competent contributors; ≥1 approval + CI green to merge.
- **Data/verification reviewers:** contributors who verify entries against sources and the freshness/
  provenance gate; the **approver must differ from the ingester** (no self-approval) and should carry
  **local/partner knowledge** of the region where possible.
- **Licensing reviewer:** confirms per-source license clearance and correct share-alike (ODbL)
  handling on the license register before publish.
- **Privacy/PII reviewer:** signs off the operator-PII minimization decisions; enforces the
  no-recipient-data line.
- **Accessibility reviewer:** competent with assistive tech; performs manual audits.
- **Steward (last-mile owner): TBD** — owns deployment, the partner relationship, last-mile
  verification logistics, and getting the dataset into helpers' hands.
- **Partner / requestor: TO BE SECURED** — a named food-bank network, 211/AIRS center, community-action
  agency, or mutual-aid org confirming need, naming a priority region, and ideally assisting with
  human verification; ultimately endorsing/adopting.
- **Elyos governance/board:** arbitrates edge cases, license disputes, and risk-tier decisions per the
  good-deed definition.

## Dependencies & integrations

- **External data sources:** government open-data portals, OpenStreetMap (ODbL), Open Referral/HSDS
  publishers, operators' published pages, partner-provided lists (all license-checked, read-only).
- **Open geocoder + map tiles:** OSM/Nominatim, Pelias, or US Census Geocoder (open-only); openly-
  licensed map tiles with attribution. No provider whose terms forbid storing geocode results.
- **Standards:** Open Referral **HSDS / HSDA** specification (the canonical schema + the interop seam).
- **Tooling/libraries:** TypeScript/ESM, pnpm; `ajv` schema validation; a lightweight a11y-friendly UI
  framework + a map library; test stack (Vitest, Playwright, axe-core/pa11y); static hosting.
- **Elyos pieces:** Task schema (`packages/schema`), CLI workspace prep / PR flow (donated lane),
  optional `packages/runner` for an **escrow-capped funded** batch-ingestion sub-lane, good-deed
  definition & risk-tier governance, review/sign-off process. Overlaps with `community-resource-maps`
  (Track 6) — coordinate to share the schema/verification engine rather than fork it.
- **Human/expert dependency:** data/verification reviewers, a licensing reviewer, and a partner org
  for last-mile human verification — the gating non-software dependencies.

## Risks & mitigations

| Risk | Likelihood | Impact | Mitigation | Owner |
| --- | --- | --- | --- | --- |
| Stale/wrong entry sends a food-insecure person to a closed/moved/nonexistent site | High | High | Freshness SLA + verification gate; closure handling (demote/withhold, never silently keep); "last verified / call ahead" surfaced; quarterly accuracy audit | Data/verification reviewer |
| License violation, esp. ODbL share-alike on OSM-derived data | Medium | High | License register + ADR #2 resolution; per-record `data_license`; conservative default (treat OSM-derived as ODbL); never relabel ODbL as CC-BY; export states effective license | Licensing reviewer |
| Publishing operator PII (home address/personal phone of a volunteer pantry) | Medium | High | PII-review step in verification; prefer public org channels; opt-out / approximate-area option; `pii_review` recorded | Privacy reviewer |
| Accidentally collecting recipient/client data | Low | High | Schema cannot represent recipient fields; no accounts/demand-side; CI PII audit | Maintainer |
| No partner org secured (verified need unconfirmed) | Medium | High | Pursue named partner from M0 in parallel; **6-month-post-M3 decision point** to declare "Publicly Shipped (generic public good)"; later endorsement upgrades status | Steward |
| Data goes stale faster than re-verification capacity | High | High | SLA-driven staleness sweep; prioritize volatile fields; partner/community re-verification; demote rather than show stale-as-current; cap published scope to what can be maintained | Maintainer |
| Source scraping breaches a site's terms/robots | Medium | Medium | Source registry license/terms gate; prefer official feeds/APIs/operator-published facts; no proprietary-locator scraping | Licensing reviewer |
| Geocoding errors place an entry at the wrong location | Medium | High | Open geocoder with recorded confidence; flag low-confidence for human check; cross-check against source address; audit sample | Maintainer |
| Drift into benefits/eligibility advice or partisan framing | Low | High | "Informational, not advice" frame; reproduce operators' own facts with citation; non-partisan rule; high-risk advisory split out separately | Maintainer |
| Duplicate/merged entries corrupt data or hide a closure | Medium | Medium | Dedup with precedence + full source trail; closure is per-source and surfaced; audit | Data reviewer |
| Maintainer bandwidth / bus factor | Medium | Medium | Reviewer rotation, documented pipeline, MIT/open data lower lock-in; coordinate with community-resource-maps | Maintainer |

## Security & privacy

**Threat surface.** As a static dataset + no-recipient-PII front end, the attack surface is small.
Principal concerns: (1) supply-chain risk in dependencies, (2) ingesting malicious/poisoned source
data (a bad actor planting a fake "food bank" to lure or scam vulnerable people), (3) operator-PII
exposure, (4) license contamination, (5) third-party tracker/script creep, (6) hostile forks implying
false endorsement.

**Controls.**
- **No secrets** in the app or repo (per Elyos rule). The funded batch sub-lane (if used) keeps its
  API key only in `packages/runner` env, never in logs/receipts/commits, under a hard escrow cap.
- **No recipient PII, by construction:** the schema cannot represent client data; no accounts, no
  demand-side capture. **No telemetry/trackers** in the view, enforced by CSP and a runtime
  network-interception E2E that fails on unexpected egress, plus a static audit.
- **Source-poisoning defense:** the verification gate (authoritative source or ≥2 independent signals)
  and human-required confirmation for high-trust methods make it hard to inject a fake site; low-
  confidence/single-source entries are not published as authoritative; anomalies (new unverified site,
  mismatched geocode) are flagged for review.
- **Operator-PII protection:** PII-review step, opt-out / approximate-area option, public-channel
  preference.
- **Supply chain:** pinned/locked deps (pnpm lockfile), dependency review/audit, minimal deps, SRI
  where applicable.
- **Integrity & provenance:** every entry source-cited and verification-logged; export bundles
  checksummed; license register kept current.
- **Abuse/misuse:** disclaimer that the dataset is community-maintained and not an official agency
  product unless a named partner endorses it; license/attribution terms documented; "call ahead /
  last verified" framing reduces reliance on possibly-stale data.

## Sustainability & maintenance

- **Ownership after delivery:** the **maintainer** owns schema/pipeline/releases; the **steward** owns
  deployment, the partner relationship, and last-mile verification logistics. Both are **TBD** and must
  be named before M3.
- **Freshness is the maintenance burden, by design.** A re-verification cadence (SLA-driven staleness
  sweeps + scheduled re-checks of volatile fields) is the core ongoing work; published scope is
  deliberately capped to what the team/partner can keep fresh. Stale entries are demoted/withheld, not
  shown as current.
- **Outcomes tracking:** because we collect no recipient telemetry, adoption/reach is tracked via
  **partner self-report**, downstream-reuser reports, and the quarterly accuracy audit — an explicit
  privacy/measurement trade-off.
- **Low lock-in:** MIT code, open data (ODbL/CC-BY per source), HSDS interoperability, and static
  hosting keep the project forkable, reusable, and cheap to run. HSDS conformance means a partner or
  211 can adopt the data without bespoke glue.

## Open questions

1. **Partner org & priority region:** Who is the named partner (food-bank network / 211 / mutual aid),
   and which region is first? Needs a human decision before *Definition of Shipped*.
2. **Last-mile human verification:** Who performs the human-required confirmation (partner staff,
   trained volunteers, a verification rota)? This is the binding non-software dependency for accuracy.
3. **Data-license resolution (ADR #2):** ODbL-only, CC-BY-without-OSM, or dual-track? This shapes what
   downstream reusers can do and must be decided before ingestion.
4. **Freshness SLA windows:** Are 90/120/180-day defaults right, or partner-/source-specific?
5. **Operator-PII policy:** Final opt-out mechanism and threshold for "approximate area only."
6. **Funded sub-lane:** Do we use an escrow-capped `packages/runner` batch for large ingestion, and
   what is the per-task budget cap?
7. **Eligibility content boundary:** Confirm we stay informational-only; if any eligibility *advice*
   is ever requested, it must be split into a separate **high-risk** module with licensed-professional
   review and "informational, not legal advice" framing — out of scope here.
8. **Coordination with `community-resource-maps`:** share one schema/verification engine vs. separate.

## References

- Elyos work rules — `C:\code\elyos\CLAUDE.md`
- Good-deed definition & risk tiers — `C:\code\elyos\docs\good-deed-definition.md`
- Task schema — `C:\code\elyos\packages\schema\src\schemas.ts`
- Portfolio roadmap (this project: Track 10; overlaps Track 6 `community-resource-maps`) —
  `C:\code\elyos\planning\ROADMAP.md`
- Open Referral — **Human Services Data Specification (HSDS)** and **HSDA** API (the canonical schema +
  interop standard).
- OpenStreetMap — **Open Database License (ODbL) 1.0** (attribution + database share-alike).
- Data sources (license-checked per use): government open-data portals (federal PD; state/local vary),
  USDA program site listings, operators' own published pages, partner-provided lists.
- Open geocoders: OSM/Nominatim, Pelias, US Census Geocoder (open-only).
- WCAG 2.2 AA (W3C Web Content Accessibility Guidelines).

---

## Appendix A — Improvements applied

The following 25 specific improvements were identified against the first draft and are **applied**
in the body above (not merely proposed).

1. **Adopted HSDS (Open Referral) as the canonical schema** instead of a bespoke model, so the data is
   interoperable with 211/AIRS and civic tech (§Executive summary, §6, §12).
2. **Elevated freshness to a safety property** with an explicit analogy to mis-stated safety advice,
   making "no entry published without recent verification" the project's spine (§Executive summary,
   §8).
3. **Resolved the ODbL share-alike tension head-on** with three concrete options and a conservative
   default, rather than asserting a blanket CC-BY license that would violate OSM's terms (§7, ADR #2).
4. **Made the schema structurally incapable of holding recipient PII** (no client fields exist), so
   client-data leakage is impossible by construction, not just by policy (§6, §7, §14).
5. **Added an operator-PII minimization step** (home-address/personal-phone risk for volunteer
   pantries) with an opt-out / approximate-area option and a recorded `pii_review` (§7, §14).
6. **Specified a concrete verification policy** (one authoritative source OR ≥2 independent signals;
   human-required methods) so "verified" has a checkable definition (§6, §8).
7. **Forbade AI self-certification** of human-only verification methods (phone/operator/partner
   confirmation), aligning with the CLAUDE.md rule that the CLI never runs headless verification (§6, §8).
8. **Added a staleness state machine** (flag at 120d, demote/withhold at 180d) and **first-class
   closure handling** so contradicted entries are demoted, never silently kept (§6, §8).
9. **Defined a quarterly random-sample accuracy audit** with a measurable ≥95% target, independent of
   the ingester (§4 metrics, §8, M3).
10. **Replaced vanity metrics with accuracy/freshness-weighted outcomes** and stated explicitly that a
    large stale directory is a liability, not an outcome (§4).
11. **Added a source registry + license register as the pipeline's front door** — no source is
    ingested before its license is cleared (§6 stage 1, §7).
12. **Constrained geocoding to open geocoders** whose terms permit storing results, with recorded
    geocode confidence and low-confidence flagging (§6, §Risks).
13. **Added a source-poisoning threat** (fake "food bank" to lure/scam vulnerable people) and tied the
    verification gate to its mitigation (§14).
14. **Reinforced the non-partisan + "informational, not advice" boundary** and explicitly deferred
    eligibility *advice* to a separate high-risk module / the `benefits-navigator` project (§3, §7, §8).
15. **Honest partner status throughout** — `verifiedNeed = false`, `requestor = "TBD"`, "TO BE SECURED"
    — with a 6-month-post-M3 "Publicly Shipped (generic public good)" decision point so a finished
    dataset isn't stranded (§Executive summary, §2, §8).
16. **Per-record `data_license` + `shareAlike` flag drive license resolution automatically**, and the
    export bundle states the effective license(s) + required attributions (§6 data model, §7).
17. **Made dedup keep the full source trail with a precedence rule** so merges can't hide a closure or
    erase provenance (§6, §Risks).
18. **Defined an optional escrow-capped funded sub-lane** for large batch ingestion via
    `packages/runner`, with a hard per-task budget cap, per Elyos funded-lane rules (§6, §12, §14).
19. **Added a runtime no-egress E2E + CSP** (not just a static grep) to enforce zero trackers on the
    view, matching the house standard (§4, §14).
20. **Specified the accessibility support matrix and cadence** (NVDA/JAWS/VoiceOver/TalkBack +
    keyboard, before each milestone/release) rather than a vague "a11y pass" (§8, M2).
21. **Sequenced ADR #1 (HSDS) and ADR #2 (license) before schema finalization and ingestion**, with the
    schema marked provisional until they land (§6 decision ordering, M0).
22. **Called out and scoped coordination with `community-resource-maps`** (Track 6 overlap) to share
    one schema/verification engine rather than fork (§12, §Open questions).
23. **Added closure/"call ahead / last verified" surfacing in the UI** so users never over-trust a
    possibly-stale entry, reducing reliance harm (§6, §8, §14).
24. **Capped published scope to maintainable freshness** — coverage scales only as fast as
    re-verification capacity — turning a sustainability risk into a stated design rule (§3, §15, §Risks).
25. **Distinguished volatile vs. stable fields in the SLA** (hours/eligibility/status re-checked far
    more often than name/location) so re-verification effort targets what actually changes (§6).

## Review sign-off

**Reviewer pass (self-review against PLAN_SPEC.md, CLAUDE.md, the good-deed definition, and the Task
schema). Findings and fixes:**

- **Structure check:** all **17 required H2 sections** are present and in the specified order
  (Executive summary → References), each with metadata header. ✔
- **Honesty check:** partner/verified-need is consistently **TO BE SECURED** (`verifiedNeed = false`,
  `requestor = "TBD"`); no fabricated partner or invented endorsement. ✔
- **License/provenance check:** the ODbL share-alike gate is treated as a headline decision with a
  conservative default; per-record license + provenance are mandatory; CC-BY is **not** asserted over
  OSM-derived data. Fixed an earlier draft implication that the whole dataset could be CC-BY. ✔
- **Privacy check:** recipient PII is impossible by schema; operator PII is minimized with opt-out;
  no telemetry. ✔
- **Risk-tier check:** correctly **medium** (locates services / reproduces published facts), with the
  high-risk advisory boundary (eligibility advice) explicitly excluded and routed to a separate
  licensed-review module — matching the prompt's high-risk-legal guardrail without over-classifying
  this project. ✔
- **Guardrail check:** non-partisan + "informational, not advice" framing applied to all
  program/eligibility content. ✔
- **Architecture-rule check:** source-specific logic confined to ingestion **adapters**; core model is
  source-neutral; the donated lane never runs headless verification (AI flags, humans confirm); funded
  sub-lane is escrow-capped — consistent with CLAUDE.md. ✔
- **Outcomes check:** metrics are beneficiary-centric with baselines/targets and a privacy-preserving
  measurement method; accuracy/freshness weighted above breadth. ✔
- **Correction applied during review:** added the explicit **"strongest verification methods require a
  human"** rule to §6/§8 (the draft had implied AI could set `last_verified`), closing a real
  integrity gap. ✔
- **Correction applied during review:** added **geocoding-error** as a distinct High-impact risk (it
  had been folded into freshness) with its own confidence-flagging mitigation. ✔
- **Consistency check:** TASKS.md milestones (M0–M3), IDs, reviewers, and the Definition of Shipped
  match this plan; the example Task JSON validates against `packages/schema/src/schemas.ts` (all
  required fields present; enums valid; `verifiedNeed = false`). ✔

**Sign-off:** Plan is internally consistent, schema-aligned, and honest about unknowns. Ready for
maintainer/board review. Outstanding human decisions are enumerated in §Open questions (partner,
region, license ADR, SLA windows, funded-lane budget).
