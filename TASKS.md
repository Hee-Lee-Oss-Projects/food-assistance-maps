# TASKS — food-assistance-maps (open, freshness-tracked food-assistance directories)

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- `id` — stable slug ID, e.g. `food-maps-schema-001`.
- `title` — the task title from the table.
- `project` — `"food-assistance-maps"`.
- `type` — one of `code | research | writing | data | design-spec | maintenance` (the "Type" column).
- `lane` — `"donated"` for all tasks here (no escrow/API spend). The optional **funded** batch-ingestion
  sub-lane (see backlog) would set `lane: "funded"` and **require `fundedBudgetUsd`** (a hard cap).
- `priority` — `high | medium | low`.
- `domain` — array, e.g. `["food-security","open-data","civic-tech","accessibility"]`.
- `riskTier` — `low | medium | high` (the "Risk" column). Data/entry + content tasks are **medium**
  (a wrong/stale entry can harm a food-insecure person); pure scaffolding/tooling is **low**.
- `urgent` — boolean (default `false`; this is a maintained directory, not live emergency response).
- `deliverable` — `pr | dataset | document | translation` (the "Deliverable" column).
- `tokenEstimate` — `small | medium | large` (the "Size" column).
- `status` — `open | in-progress | review | delivered | done` (all start `open`).
- `context`, `objective`, `acceptanceCriteria[]`, `output` — task narrative + checkable criteria.
- `resources[]` — source/reference URLs or repo paths.
- `requestor` — partner/requestor (**TO BE SECURED**; use `"TBD"` until a partner is named).
- `verifiedNeed` — **`false` until a named partner org confirms need in writing** (honest default).
- `outputLicense` — `"MIT"` for code; **data license is resolved per source** (ADR #2): `"ODbL-1.0"`
  for any OSM-derived database, `"CC-BY-4.0"` for permissive/PD/original-verification data,
  `"CC-BY-4.0"` for documents/ADRs.

**Reviewer column legend:** `maintainer` (code review), `data/verify` (data + verification reviewer,
distinct from the ingester — local/partner knowledge preferred), `license` (licensing reviewer for
per-source clearance + ODbL share-alike), `privacy` (operator-PII + no-recipient-data review),
`a11y` (accessibility reviewer), `steward` (last-mile / partner owner).

**No self-approval rule (all `data/verify` tasks):** the contributor who ingested/verified an entry
and the reviewer who approves it must be **distinct people**. Human-required verification methods
(`phone-confirmed` / `operator-confirmed` / `partner-confirmed`) need a **human** attribution; an AI
session may extract, corroborate across sources, and flag, but **may not self-certify** those methods.
Each approval writes a **PR-tied, append-only verification-log entry** (PR #, commit SHA, entries
covered, sources + licenses checked, dedup/closure/PII decisions). This is the auditable record the
Definition of Shipped checks against.

**Sequencing rule (M0):** **ADR #1 (adopt HSDS)** and **ADR #2 (data-license / ODbL share-alike)** are
decided in `arch-002` **before** the schema (`schema-001`/finalization) and any ingestion. Until then
the schema is provisional and ingestion tasks are blocked.

---

## Milestone M0 — Foundation & cold-start

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| food-maps-arch-002 | ADRs: **adopt HSDS**, **data-license/ODbL share-alike resolution (decide first)**, freshness SLA + staleness state machine, verification policy, open-geocoder + map tiles, UI/hosting | design-spec | small | low | document | — | maintainer, license |
| food-maps-schema-001 | HSDS + provenance/freshness JSON Schema + TS types + `ajv` validation | data | medium | low | dataset | arch-002 | maintainer, data/verify |
| food-maps-registry-003 | Source registry + license register; clear ≥3 sources (PD/CC/ODbL/operator) | research | medium | medium | document | arch-002 | license |
| food-maps-pilot-004 | Pilot ingest+geocode+dedupe+verify ~25–50 entries in one locality, published with full provenance + `last_verified` | data | large | medium | dataset | schema-001, registry-003 | data/verify, license, privacy |
| food-maps-ci-005 | CI gates: lint, typecheck, unit, schema-validation, freshness report, no-recipient-PII/no-tracker audit | code | medium | low | pr | schema-001 | maintainer |
| food-maps-view-006 | Minimal accessible directory view rendering the pilot from published exports | code | medium | low | pr | pilot-004, ci-005 | maintainer, a11y |

**Acceptance criteria — key tasks**

- **food-maps-arch-002 (ADRs):**
  - **ADR #1** adopts Open Referral **HSDS** as the canonical schema and defines the Elyos
    provenance/freshness extension shape.
  - **ADR #2 (decided before schema/ingestion)** resolves the **ODbL share-alike** question with one
    explicit option (ODbL-only / CC-BY-without-OSM / dual-track) and a conservative default; states how
    `data_license` is set per record and how export bundles declare effective license + attribution.
  - Freshness SLA windows (volatile vs. stable fields), the staleness state machine
    (flag→demote→withhold), closure handling, and the verification policy (one authoritative source OR
    ≥2 independent signals; human-required methods) are recorded.
  - Open-geocoder + map-tile choice (open-only, storage-permitting terms) and UI/hosting recorded.
- **food-maps-schema-001 (schema):**
  - Schema captures HSDS core (organization/service/location/schedule/service_area/contact) **plus**
    `sources[]` (name/url/license/retrievedDate/attribution/shareAlike), `verification_method`,
    `verified_by`, `last_verified`, `reverify_due`, `confidence`, `operating_status`, `pii_review`,
    `data_license`, `reviewLogRef`.
  - **No recipient/client fields exist** (cannot represent name/DOB/benefits/household data).
  - Build-time validation **rejects** an entry missing required provenance/verification/license fields,
    and **rejects** any recipient-PII-shaped field.
  - Follows ADR #1/#2; schema is provisional until those ADRs land.
- **food-maps-registry-003 (source registry + license register):**
  - ≥3 sources registered with name, URL, **license/terms**, attribution string, `shareAlike` flag,
    retrieval method, and **PII risk** assessment; only license-cleared sources are marked ingestible.
  - OSM recorded as **ODbL (attribution + share-alike)**; US federal sources as PD; state/local and
    HSDS publishers verified individually (not assumed open).
  - No source whose terms/robots forbid the intended reuse is marked ingestible.
- **food-maps-pilot-004 (verified pilot slice):**
  - ~25–50 entries in one locality ingested, geocoded (open geocoder, confidence recorded), deduped
    (precedence + full source trail), and **each published entry clears the verification policy** with
    `last_verified` set and a `verification_method`; human-required methods carry a **human**
    `verified_by` (no AI self-certification).
  - Every entry has complete provenance and a correct per-record `data_license` (ODbL honored for any
    OSM-derived record); operator-PII reviewed (`pii_review` set; opt-out/approximate-area where a
    private individual's address/phone is implicated).
  - Closed/moved sites are marked, not dropped silently; low-confidence geocodes flagged for review.
  - Approved by a reviewer **distinct from the ingester**; PR-tied verification-log entry written.
- **food-maps-ci-005 (CI gates):**
  - CI fails on lint/type/unit errors and on **schema-validation** failure.
  - A **freshness report** is produced (counts within/over SLA); a **no-recipient-PII / no-tracker**
    audit uses defense in depth — static audit **plus** a runtime network-interception E2E on the view
    that fails on any unexpected outbound request (CSP enforced). A static grep alone does not pass.

**Definition of Done (M0):** HSDS + license ADRs recorded **before** schema finalization; schema +
`ajv` validation published with no recipient-PII representable; source/license register with ≥3
cleared sources; a ~25–50-entry pilot ingested, verified to policy, license-correct, PII-reviewed, and
published with full provenance + `last_verified`; CI enforces schema/freshness/PII/tracker gates; a
minimal accessible view renders the pilot.

---

## Milestone M1 — Verification & freshness pipeline (safety core)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| food-maps-ingest-007 | Ingestion adapters for ≥3 source types (gov open data, OSM, HSDS feed), source-neutral core | code | large | medium | pr | schema-001, registry-003 | maintainer, license |
| food-maps-geocode-008 | Open-geocoder integration + dedup/reconciliation with precedence + confidence flagging | code | large | medium | pr | ingest-007 | maintainer, data/verify |
| food-maps-fresh-009 | Staleness state machine (flag/demote/withhold) + closure handling + freshness report | code | medium | medium | pr | schema-001 | maintainer, data/verify |
| food-maps-verify-010 | Verification-log enforcement in CI (no publish without provenance+method+distinct reviewer) | code | medium | medium | pr | fresh-009, ci-005 | maintainer, data/verify |
| food-maps-privacy-011 | Operator-PII review workflow (opt-out / approximate-area; `pii_review` recorded) | design-spec | small | medium | document | schema-001 | privacy |

**Acceptance criteria — key tasks**

- **food-maps-ingest-007 (adapters):**
  - One isolated adapter per source type; **all source-specific logic lives in the adapter**, the core
    HSDS model stays source-neutral (per Elyos architecture rules).
  - Each adapter records `sources[]` with license + `shareAlike`; stores **structured facts only** (no
    verbatim copyrighted prose); respects each source's terms/robots.
- **food-maps-fresh-009 (staleness + closure):**
  - Entries past the SLA are auto-flagged (`flag` at the configured window) and **demoted/withheld**
    from "authoritative" past the demote window; a contradicted entry is demoted, **never silently
    kept**.
  - Closure is first-class: `operating_status` of `temporarily_closed`/`moved`/`closed` is surfaced;
    the published view shows `last_verified` and a "call ahead" caveat.
  - The freshness report shows ≥90% of pilot-region entries within SLA.
- **food-maps-verify-010 (CI verification gate):**
  - CI blocks publish of any entry lacking complete provenance, a `verification_method`, or a
    `verified_by`; blocks where the approver equals the ingester (no self-approval).
  - Human-required methods cannot be set by an automated pipeline step (asserted in CI).
- **food-maps-privacy-011 (operator-PII workflow):**
  - Documented decision flow: prefer public org channel; flag plausibly-private addresses/phones;
    opt-out → approximate-area + contact; record `pii_review`. No recipient data path exists.

**Definition of Done (M1):** ≥3 source adapters feeding a source-neutral core; open-geocoder + dedup
with confidence/precedence; staleness state machine + closure handling live with ≥90% pilot freshness;
verification-log gate enforced in CI (provenance + method + distinct approver, no AI self-cert);
operator-PII workflow operational.

---

## Milestone M2 — Coverage, accessibility & open distribution

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| food-maps-region-012 | Fully cover + verify one region; reach ≥95% audit accuracy | data | large | medium | dataset | geocode-008, fresh-009, verify-010 | data/verify, license, privacy |
| food-maps-export-013 | Open exports: HSDS JSON/CSV + GeoJSON + documented schema + effective-license/attribution statement | code | medium | medium | pr | region-012 | maintainer, license |
| food-maps-a11y-014 | Directory/map view to WCAG 2.2 AA (automated + manual AT audit) + search/filter | code | large | low | pr | view-006, export-013 | a11y, maintainer |
| food-maps-audit-015 | Quarterly random-sample re-verification audit process + tooling | maintenance | small | medium | document | verify-010 | data/verify, steward |

**Acceptance criteria — key tasks**

- **food-maps-region-012 (full region):**
  - One region's food-assistance services are complete to a stated coverage definition; every published
    entry clears the verification + freshness + license + PII gates; a random-sample audit shows
    **≥95% accuracy** (existence/location/hours/eligibility) and **≥90% within freshness SLA**.
- **food-maps-export-013 (open exports):**
  - HSDS JSON/CSV + GeoJSON published with a documented schema; the bundle states the **effective
    license(s)** per the ODbL resolution (ODbL share-alike honored for OSM-derived records; CC-BY for
    permissive/PD-derived) and **required attributions** (incl. "© OpenStreetMap contributors, ODbL").
  - No CC-BY relabeling of ODbL-derived data.
- **food-maps-a11y-014 (accessible view + search):**
  - WCAG 2.2 AA: automated axe/pa11y 0 critical issues **and** a documented manual AT audit across the
    support matrix (NVDA+Firefox/Win, JAWS+Chrome/Win, VoiceOver+Safari/macOS·iOS, TalkBack+Chrome/
    Android, keyboard-only per desktop browser), signed off by the a11y reviewer.
  - Search/filter by location, service type, hours, language, accessibility; map is keyboard-operable
    with a non-map fallback list; `last_verified`/"call ahead" surfaced on every entry.

**Definition of Done (M2):** ≥1 region fully covered at ≥95% audit accuracy + ≥90% freshness; HSDS/CSV/
GeoJSON exports published with correct per-source licensing + attribution; accessible (AA, automated +
manual) directory/map view with search/filter; quarterly audit process defined.

---

## Milestone M3 — Partner adoption, audit & sustainability

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
| --- | --- | --- | --- | --- | --- | --- | --- |
| food-maps-partner-016 | Secure named partner (food-bank network / 211 / mutual aid); confirm region + last-mile verification | research | medium | medium | document | — | maintainer, steward |
| food-maps-outcomes-017 | Outcomes-tracking process (partner self-report; no client telemetry) + reuser register | maintenance | small | low | document | partner-016 | steward |
| food-maps-deploy-018 | Production deployment + versioned exports + disclaimer/attribution/branding | code | medium | low | pr | a11y-014, export-013 | maintainer, steward |
| food-maps-recadence-019 | Documented re-verification cadence + SLA-driven staleness sweep automation | maintenance | medium | medium | document | fresh-009, audit-015 | data/verify, steward |

**Acceptance criteria — key tasks**

- **food-maps-partner-016 (secure partner):**
  - A named food-bank network / 211-AIRS center / community-action agency / mutual-aid org confirms the
    need in writing (letter of support or MOU), names a priority region, and (ideally) commits to
    **last-mile human verification**.
  - On success, `verifiedNeed` flips to `true` and `requestor` is set to the named org across tasks.
- **food-maps-outcomes-017 (outcomes + reuse):**
  - Privacy-preserving outcomes process using a standard template (partner, period, channels,
    **confirmed successful referrals/look-ups**, flagged estimates, regions/languages, de-dup notes)
    over a rolling 12-month window; counts distinct people once per period; **no client PII collected**.
  - A reuser register tracks downstream integrations (211 import, community maps, apps).
- **food-maps-deploy-018 (production deploy):**
  - Static production build + versioned export bundles over HTTPS; visible disclaimer that the dataset
    is community-maintained and **not an official agency product unless a named partner endorses it**;
    attribution/trademark terms present; "last verified / call ahead" framing on entries.
- **food-maps-recadence-019 (re-verification cadence):**
  - Documented cadence (volatile fields re-checked far more often than stable); SLA-driven staleness
    sweep flips overdue entries to "needs re-verification" and surfaces them to humans/partner; scope
    is capped to maintainable freshness.

**Definition of Done (M3):** ≥1 **named partner** endorsement/adoption on file (`verifiedNeed = true`);
quarterly accuracy audit meeting ≥95% accuracy + ≥90% freshness; outcomes-tracking + reuser register
operational; production deployment with disclaimer/attribution; re-verification cadence + staleness
sweep running. This satisfies the project-level *Definition of Shipped (partner-adopted)*.

**Decision point (so a finished dataset isn't stranded):** if no partner is secured by **6 months
after the M3 production build is ready**, the steward + Elyos governance may declare **"Publicly Shipped
(generic public good)"** — Definition-of-Shipped criteria (1)–(5) met, deployed and offered via
mutual-aid/civic channels, accuracy/freshness maintained by best-effort community re-verification. A
later partner endorsement upgrades the status rather than re-opening launch.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
| --- | --- | --- | --- | --- | --- | --- |
| food-maps-funded-020 | **Funded** escrow-capped batch ingestion for a large region (`packages/runner`) | data | large | medium | dataset | `lane: funded`; requires `fundedBudgetUsd` hard cap; key only in runner env |
| food-maps-i18n-021 | Localize the view + key fields into priority community languages | writing | medium | medium | translation | Partner-driven language priority; safety-accuracy review |
| food-maps-hsda-022 | Optional read-only HSDA-style API over the published dataset | code | medium | low | pr | Static API is the default; HSDA endpoint if a reuser needs it |
| food-maps-submit-023 | Moderated operator/community submission flow (still gated by provenance + verification) | code | medium | medium | pr | No unverified publish; human approval required |
| food-maps-merge-024 | Coordinate/merge schema + verification engine with `community-resource-maps` | design-spec | small | low | document | Avoid forking the Track 6 overlap |
| food-maps-extras-025 | Extend coverage to community fridges / school+summer meal sites with their own freshness rules | data | medium | medium | dataset | Volatile schedules; tighter SLA |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task. `verifiedNeed` is `false` and `requestor` is
`"TBD"` because **no partner org is secured yet** (honest default per the plan). The ADR task is the
true first step (it gates the schema); shown here as the canonical example.

```json
{
  "id": "food-maps-arch-002",
  "title": "ADRs: adopt HSDS, resolve data-license/ODbL share-alike, freshness SLA, verification policy, geocoder, UI/hosting",
  "project": "food-assistance-maps",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["food-security", "open-data", "civic-tech", "licensing"],
  "riskTier": "low",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "small",
  "status": "open",
  "context": "food-assistance-maps publishes an open, freshness-tracked directory of food banks, pantries, community fridges, and meal programs by region. Before any data is ingested, the project must lock its foundational decisions: the canonical schema, the data-license resolution (OpenStreetMap is ODbL share-alike, so a blanket CC-BY claim would violate its terms), the freshness SLA and staleness behavior, the verification policy, and the open-geocoder/UI choices. These ADRs gate the schema and all ingestion.",
  "objective": "Produce Architecture Decision Records that (1) adopt Open Referral HSDS as the canonical schema plus an Elyos provenance/freshness extension, (2) resolve the ODbL share-alike vs. CC-BY question with a conservative default and a per-record data_license rule, (3) define the freshness SLA windows + staleness state machine + closure handling, (4) define the verification policy (one authoritative source OR >=2 independent signals; human-required confirmation methods), and (5) choose an open-only geocoder, map tiles, UI framework, and static hosting.",
  "acceptanceCriteria": [
    "ADR #1 adopts HSDS as canonical and specifies the provenance/freshness extension fields",
    "ADR #2 resolves data licensing (ODbL-only / CC-BY-without-OSM / dual-track) with a conservative default and a rule for setting data_license per record and declaring effective license + attribution in exports",
    "Freshness SLA windows (volatile vs. stable fields), the flag/demote/withhold staleness state machine, and first-class closure handling are recorded",
    "Verification policy is defined: authoritative-source vs. >=2-signal threshold, and which methods require a human (AI may not self-certify phone/operator/partner confirmation)",
    "Open-only geocoder + map-tile provider (terms permit storing results), UI framework, and static hosting are chosen and justified",
    "ADR #1 and ADR #2 are explicitly marked as prerequisites for schema finalization and ingestion"
  ],
  "resources": [
    "C:\\code\\elyos\\planning\\projects\\food-assistance-maps\\PLAN.md",
    "C:\\code\\elyos\\packages\\schema\\src\\schemas.ts",
    "https://docs.openreferral.org/",
    "https://opendatacommons.org/licenses/odbl/"
  ],
  "output": "A document of Architecture Decision Records (HSDS adoption, data-license/ODbL resolution, freshness SLA + staleness machine, verification policy, geocoder/UI/hosting) that gate the schema and ingestion work.",
  "requestor": "TBD",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```

---

## Generated task index

> Auto-generated by the Elyos task-decomposition agent on 2026-06-29.
> Every TASKS.md backlog row now has a corresponding `tasks/<id>.json` file validated against the
> Elyos taskSchema (25 files total; 1 pre-existing seed kept as-is, 24 generated).

| File | Title | Milestone | Type | Lane | Status |
| --- | --- | --- | --- | --- | --- |
| [food-maps-arch-002.json](tasks/food-maps-arch-002.json) | ADRs: adopt HSDS, data-license/ODbL, freshness SLA, verification policy, geocoder, UI/hosting | M0 | design-spec | donated | open |
| [food-maps-schema-001.json](tasks/food-maps-schema-001.json) | HSDS + provenance/freshness JSON Schema + TS types + ajv validation | M0 | data | donated | open |
| [food-maps-registry-003.json](tasks/food-maps-registry-003.json) | Source registry + license register; clear ≥3 sources | M0 | research | donated | open |
| [food-maps-pilot-004.json](tasks/food-maps-pilot-004.json) | Pilot ingest, geocode, dedupe, and verify ~25–50 entries with full provenance | M0 | data | donated | open |
| [food-maps-ci-005.json](tasks/food-maps-ci-005.json) | CI gates: lint, typecheck, unit, schema-validation, freshness report, no-PII/no-tracker audit | M0 | code | donated | open |
| [food-maps-view-006.json](tasks/food-maps-view-006.json) | Minimal accessible directory view rendering the pilot from published exports | M0 | code | donated | open |
| [food-maps-ingest-007.json](tasks/food-maps-ingest-007.json) | Ingestion adapters for ≥3 source types (gov open data, OSM, HSDS feed) | M1 | code | donated | open |
| [food-maps-geocode-008.json](tasks/food-maps-geocode-008.json) | Open-geocoder integration + dedup/reconciliation with precedence + confidence flagging | M1 | code | donated | open |
| [food-maps-fresh-009.json](tasks/food-maps-fresh-009.json) | Staleness state machine (flag/demote/withhold) + closure handling + freshness report | M1 | code | donated | open |
| [food-maps-verify-010.json](tasks/food-maps-verify-010.json) | Verification-log enforcement in CI (no publish without provenance + method + distinct reviewer) | M1 | code | donated | open |
| [food-maps-privacy-011.json](tasks/food-maps-privacy-011.json) | Operator-PII review workflow (opt-out / approximate-area; pii_review recorded) | M1 | design-spec | donated | open |
| [food-maps-region-012.json](tasks/food-maps-region-012.json) | Fully cover and verify one region; reach ≥95% audit accuracy | M2 | data | donated | open |
| [food-maps-export-013.json](tasks/food-maps-export-013.json) | Open exports: HSDS JSON/CSV + GeoJSON + documented schema + effective-license/attribution | M2 | code | donated | open |
| [food-maps-a11y-014.json](tasks/food-maps-a11y-014.json) | Directory/map view to WCAG 2.2 AA with automated + manual AT audit and search/filter | M2 | code | donated | open |
| [food-maps-audit-015.json](tasks/food-maps-audit-015.json) | Quarterly random-sample re-verification audit process and tooling | M2 | maintenance | donated | open |
| [food-maps-partner-016.json](tasks/food-maps-partner-016.json) | Secure named partner (food-bank network / 211 / mutual aid); confirm region + last-mile verification | M3 | research | donated | open |
| [food-maps-outcomes-017.json](tasks/food-maps-outcomes-017.json) | Outcomes-tracking process (partner self-report; no client telemetry) + reuser register | M3 | maintenance | donated | open |
| [food-maps-deploy-018.json](tasks/food-maps-deploy-018.json) | Production deployment + versioned exports + disclaimer/attribution/branding | M3 | code | donated | open |
| [food-maps-recadence-019.json](tasks/food-maps-recadence-019.json) | Documented re-verification cadence + SLA-driven staleness sweep automation | M3 | maintenance | donated | open |
| [food-maps-funded-020.json](tasks/food-maps-funded-020.json) | Funded escrow-capped batch ingestion for a large region via packages/runner | Backlog | data | **funded** ($25 cap) | open |
| [food-maps-i18n-021.json](tasks/food-maps-i18n-021.json) | Localize the directory view and key data fields into priority community languages | Backlog | writing | donated | open |
| [food-maps-hsda-022.json](tasks/food-maps-hsda-022.json) | Optional read-only HSDA-style API endpoint over the published dataset | Backlog | code | donated | open |
| [food-maps-submit-023.json](tasks/food-maps-submit-023.json) | Moderated operator/community submission flow (provenance + verification gate enforced) | Backlog | code | donated | open |
| [food-maps-merge-024.json](tasks/food-maps-merge-024.json) | Coordinate schema and verification engine with community-resource-maps to avoid forking | Backlog | design-spec | donated | open |
| [food-maps-extras-025.json](tasks/food-maps-extras-025.json) | Extend coverage to community fridges and school/summer meal sites with tighter freshness rules | Backlog | data | donated | open |

**Fan-out:** No fan-out was applied. TASKS.md does not enumerate specific languages (i18n-021 is partner-driven), specific regions for funded ingestion (020), or other bounded dimensions that would justify generating multiple concrete files per row. One JSON per backlog row.

**Notes:**
- `food-maps-funded-020` uses `lane: "funded"` with `fundedBudgetUsd: 25.0` (placeholder cap; steward must confirm before activation).
- `food-maps-i18n-021` is `type: "writing"` + `deliverable: "translation"` (not a separate `translation` type, which is not valid per schema).
- All tasks have `verifiedNeed: false` and `requestor: "TBD"` until a named partner is secured (food-maps-partner-016).
