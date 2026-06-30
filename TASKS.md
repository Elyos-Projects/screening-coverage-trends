# screening-coverage-trends — TASKS.md

> Status: Draft · Version: 0.1.0 · Last updated: 2026-06-28 · Owner: TBD (maintainer) · Lane: donated

Backlog for **screening-coverage-trends** (slug: `screening-coverage-trends`): a reproducible,
open pipeline that turns **open, aggregate** cancer-screening-coverage data into a harmonized
**trend dataset** + **plain-language explainers** (education only). See `PLAN.md` for full context.

**Binding cancer guardrails apply to every task** (see PLAN.md header): open/aggregate/de-identified
data only (no controlled/individual-level data); per-source license verification + provenance on
every assertion; **no medical advice** — patient-facing content is education only, "not medical
advice — consult your care team," **oncologist + patient-advocate sign-off (blocking)**, `riskTier:
high`; crisis/helpline info independently verified. This is a **medium-risk project overall** whose
**patient-facing surface is HIGH**. **No beneficiary partner and no expert reviewers are yet
secured**, so every task carries `requestor: TO BE SECURED` and `verifiedNeed: false`.

## How these tasks map to Elyos

Each task below becomes an Elyos **Task JSON** validated against
`packages/schema/src/schemas.ts`. Field mapping:

- **id** — stable slug `screening-coverage-trends-<area>-NNN` (e.g. `screening-coverage-trends-guardrails-001`).
- **title** — the task title in the milestone table.
- **project** — `screening-coverage-trends`.
- **type** — one of `code | research | writing | data | design-spec | maintenance`.
- **lane** — `donated` (default; no funded tasks planned. Any `funded` task must add
  `fundedBudgetUsd` with a hard cap).
- **priority** — `high | medium | low`.
- **domain** — array, e.g. `["cancer","public-health","open-data","data-science"]`.
- **riskTier** — `low | medium | high`. **Any patient-facing content = `high`** (oncologist +
  advocate sign-off). Data/pipeline/license tasks are `medium`; pure infra/UI is `low`.
- **urgent** — boolean (no urgent tasks at cold-start).
- **deliverable** — `pr | dataset | document | translation`.
- **tokenEstimate** — `small | medium | large` (maps to the Size column).
- **status** — `open | in-progress | review | delivered | done` (all start `open`).
- **context / objective / acceptanceCriteria[] / resources[] / output** — per task.
- **requestor** — beneficiary/steward/expert; `TO BE SECURED` where unknown.
- **verifiedNeed** — `true` only once a specific beneficiary/need is confirmed; otherwise `false`
  (currently `false` everywhere — none secured).
- **outputLicense** — open dataset: **CC-BY-4.0** (or **CC0** for US-PD-only series, governance TBD);
  code: **MIT**; docs/explainers: **CC-BY-4.0**.

Size legend: small ≈ tokenEstimate `small`, med ≈ `medium`, large ≈ `large`.
Reviewer key: **License rev.** = License + provenance reviewer (mandatory gate); **Stats rev.** =
statistical reviewer; **Clinician** = credentialed oncology clinician (HIGH, blocking, TO BE SECURED);
**Advocate** = patient-advocate reviewer (HIGH, blocking, TO BE SECURED); **Maintainer**, **Steward**.

---

## Milestone M0 — Compliance spine, license gate & cold-start foundation

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-guardrails-001 | Cancer data & compliance guardrail specification (the contract all code/content implements) | design-spec | medium | medium | document | — | Maintainer + License rev. |
| screening-coverage-trends-sources-002 | Source catalog v0 + per-source license classification + NC/SA policy decision | data | medium | medium | document | 001 | License rev. + Maintainer |
| screening-coverage-trends-repo-003 | Repo skeleton (TS/ESM/pnpm) + CI with provenance-coverage & license-status gates | code | small | low | pr | — | Maintainer |
| screening-coverage-trends-schema-004 | Canonical trend-series schema + provenance model (JSON Schema) | code | medium | medium | pr | 003 | Maintainer + Stats rev. |
| screening-coverage-trends-gate-005 | Apply gate end-to-end to ONE accepted source (US breast/mammography series) | data | small | medium | dataset | 002, 004 | License rev. + Stats rev. |
| screening-coverage-trends-outreach-006 | Beneficiary + expert-reviewer outreach (dated acquisition track) | research | small | medium | document | 001 | Maintainer + Steward |

**Acceptance criteria — key tasks**

- **screening-coverage-trends-guardrails-001** (compliance/guardrail spec)
  - Encodes the four binding guardrails as concrete, testable rules: (1) aggregate/de-identified/
    open-access data ONLY (controlled/individual-level explicitly out of scope at triage); (2)
    per-source license verification + provenance on every assertion/chart; (3) patient-facing =
    education only, `riskTier: high`, "not medical advice — consult your care team," blocking
    oncologist + advocate sign-off, no personalized/eligibility/diagnosis framing; (4) crisis/helpline
    info independently verified + dated, re-verified each release.
  - Defines the misread checklist (denominator stated; basis self-report/measured/model-based labeled;
    model-based small-area flagged; no causal verbs on associational data; no advice verbs; guideline
    body cited with version/date) and the no-causal-verb / not-medical-advice lints.
  - Defines the small-cell / re-identification rules (honor source suppression; own min-cell +
    min-denominator threshold; suppress+label never impute; no cross-dataset linkage).
  - Specifies the CI build-breakers (100% provenance coverage; license-status gate; schema validity;
    reproducible-from-snapshots; small-cell guard; helpline-verification gate) and the publish gate
    (review roles + blocking HIGH sign-off).
  - Reviewed and recorded; becomes the contract that tasks 003–005 and all M3 content implement.

- **screening-coverage-trends-sources-002** (source catalog + license classification + NC/SA policy)
  - Enumerates candidate sources with publisher, indicator coverage, aggregation level, vintage, and
    **verified license** sorted into: **accepted** (US PD: BRFSS/PLACES/NHIS/State Cancer Profiles;
    UK OGL v3; Eurostat; OECD verify-per-table; AIHW; StatCan), **cite-only/restricted** (WHO GHO
    CC BY-NC-SA 3.0 IGO; IARC/GLOBOCAN NC), **excluded** (CRUK, NCQA/HEDIS, any controlled/individual
    data).
  - Records, per accepted source, `license.permitsDerivatives: true` with the cited clause/URL; missing
    evidence → cite-only/exclude, never default-allow.
  - **NC/SA policy decision merged**: a fixed rule for whether NC/SA sources are ever redistributed
    (default: cite-only/exclude) so triage applies a rule, not ad-hoc judgement.
  - No individual-level/controlled source appears as in-scope (guardrail 1).

- **screening-coverage-trends-schema-004** (canonical schema + provenance model)
  - JSON Schema for the trend-series model: indicator, measure, population (age/sex/race-eth/income/
    insurance/urban-rural), geography, period, value{pct,ciLow,ciHigh,basis}, denominatorDef,
    suppression{suppressed,rule}, source{url,version,retrievedAt,license,snapshotRef}, caveats[],
    provenanceComplete, harmonizationScore.
  - `basis` enum (self-report|measured|model-based) and `suppression` are required; a series without
    a resolvable `source` ref fails validation.
  - Provenance model includes snapshot = committed copy + SHA-256 + Wayback save URL for both data and
    license text.

- **screening-coverage-trends-gate-005** (gate proven on one source)
  - One accepted US source (BRFSS/NHIS breast-screening national series) passes the full gate: license
    verified + recorded, aggregate-only confirmed, immutable snapshot + provenance captured, schema-
    valid series emitted, harmonization score ≥ 90, suppression honored.
  - Committed gate + provenance artifact; reproducible from the snapshot in CI.

**M0 Definition of Done:** guardrail/compliance spec written + reviewed; source catalog v0 with
verified per-source licenses + the NC/SA policy merged; TS/ESM/pnpm repo + CI with
provenance-coverage and license-status gates green; canonical schema + provenance model published;
the gate proven end-to-end on one accepted US source with a committed gate/provenance artifact; ≥ 1
beneficiary and ≥ 1 expert-reviewer outreach thread opened. **No patient-facing content produced.**

---

## Milestone M1 — Reproducible pipeline + first trend dataset (one indicator, US)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-pipeline-007 | Fetch→snapshot→harmonize→trend+CI pipeline (one indicator) | code | large | medium | pr | 004, 005 | Maintainer + Stats rev. |
| screening-coverage-trends-caveats-008 | Caveat layer (self-report/measured/model-based, break-in-series, suppression) | code | medium | medium | pr | 007 | Stats rev. + License rev. |
| screening-coverage-trends-dataset-009 | First trend dataset: breast/mammography, US national + states (reproducible) | data | medium | medium | dataset | 007, 008 | Stats rev. + License rev. |
| screening-coverage-trends-datasheet-010 | Data dictionary + Datasheet-for-Datasets + machine-readable metadata | writing | small | low | document | 009 | Maintainer + License rev. |

**Acceptance criteria — key tasks**

- **screening-coverage-trends-pipeline-007** (pipeline)
  - Fetches published aggregates only, writes immutable checksummed snapshots, harmonizes to the
    canonical schema, and computes trend + confidence/credible intervals propagated from source SEs;
    no undocumented smoothing/modeling.
  - Regenerates all outputs byte-stably from cached snapshots in CI; provenance coverage 100%.
  - Aggregate-only access protocol enforced; any micro-data path rejected.

- **screening-coverage-trends-dataset-009** (first dataset)
  - Breast/mammography "up-to-date" coverage trend, US national + states, from accepted PD sources;
    every series schema-valid, provenance-covered, harmonization score ≥ 90.
  - Self-report basis labeled; break-in-series + suppression flags applied; small-cell guard enforced
    (no published cell below threshold; suppressed cells labeled, not imputed).
  - Statistical + license/provenance review passed; reproducible in CI.

**M1 Definition of Done:** the pipeline produces one indicator's US national+state trend with
uncertainty + caveats, schema-valid and reproducible-from-snapshots in CI at 100% provenance
coverage; Datasheet + data dictionary published; statistical + license review passed.

---

## Milestone M2 — Multi-indicator, multi-geography trend dataset (data, no patient-facing)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-indicators-011 | Add cervical, colorectal, lung indicators (US national + sub-national) | data | large | medium | dataset | 009 | Stats rev. + License rev. |
| screening-coverage-trends-intl-012 | Add ≥1 licensed international comparison series (UK OGL / Eurostat / OECD / AIHW / StatCan) | data | medium | medium | dataset | 011 | License rev. + Stats rev. |
| screening-coverage-trends-disparities-013 | Suppression-safe disparities breakdowns (income/insurance/race-eth/urban-rural) | data | medium | medium | dataset | 011 | Stats rev. + License rev. |

**Acceptance criteria — key tasks**

- **screening-coverage-trends-indicators-011** (four indicators)
  - Breast, cervical, colorectal, and lung screening coverage trends, US national + sub-national,
    each with consistent definitions/denominators, basis labels, uncertainty, suppression, and
    provenance; harmonization score ≥ 90 per series.
  - Lung (high-risk-eligible denominator) handled with its distinct eligibility-defined denominator
    clearly documented; no implication that it applies to the general population.

- **screening-coverage-trends-intl-012** (international comparison)
  - ≥ 1 licensed international series added with attribution and full provenance; definition
    differences vs. US series explicitly documented (not silently compared).
  - **WHO GHO / IARC remain cite-only** per the NC/SA policy — referenced/linked, never redistributed
    as a CC-BY derivative.

- **screening-coverage-trends-disparities-013** (disparities, suppression-safe)
  - Sub-group breakdowns published only where licensed and above the min-cell/denominator threshold;
    below-threshold cells suppressed + labeled, never imputed; no cross-dataset linkage.
  - Neutral framing: context + caveats, no ranking / name-and-shame / political claim.

**M2 Definition of Done:** all four indicators across US national + sub-national plus ≥ 1 licensed
international comparison and suppression-safe disparities breakdowns, all provenance-covered,
reproducible, and statistically + license reviewed; NC/restricted sources confirmed cite-only.

---

## Milestone M3 — Plain-language explainers (HIGH, expert-gated)

> **Entry gate: oncology clinician AND patient-advocate reviewers must be secured before M3 starts.**
> Every task here is `riskTier: high` with **blocking** clinician + advocate sign-off.

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-explainer-014 | "How to read a screening statistic" explainer (education only) | writing | medium | high | document | 010 | Clinician + Advocate + Stats rev. |
| screening-coverage-trends-explainer-015 | ≥2 indicator trend explainers (education only, guidance pointers) | writing | medium | high | document | 011, 014 | Clinician + Advocate + Stats rev. |
| screening-coverage-trends-helpline-016 | Verified support/helpline + authoritative-guidance pointer set | research | small | high | document | 014 | Clinician + Advocate + Steward |

**Acceptance criteria — key tasks**

- **screening-coverage-trends-explainer-014** ("how to read a screening statistic")
  - Plain-language, education only; passes the misread checklist (denominator, basis label,
    model-based flag, no causal/advice verbs, guideline body cited with version/date).
  - Carries the persistent "general information, not medical advice — consult your care team" notice;
    gives **no** personalized recommendation, eligibility ruling, or diagnosis.
  - WCAG 2.2 AA + readability target met; **oncologist + advocate sign-off recorded** (both, blocking);
    comprehension check ≥ 80% on misread items.

- **screening-coverage-trends-explainer-015** (indicator trend explainers)
  - For ≥ 2 indicators, explains what the trend does/doesn't say, attributes guideline context to
    USPSTF/ACS/national bodies with citation + version/date (as education, not our advice), and links
    the verified guidance/support pointers.
  - Every figure provenance-linked to the M1/M2 dataset; no causal/over-claim language.
  - **Clinician + advocate sign-off recorded** (both, blocking).

- **screening-coverage-trends-helpline-016** (verified support/guidance pointers)
  - Each support/helpline pointer independently verified (number, region, hours, language) with a
    **dated verification record**; each guidance pointer cited to its authoritative body + version.
  - Clinician + advocate confirm appropriateness; re-verification cadence set (handed to M6).

**M3 Definition of Done:** the "how to read" explainer + ≥ 2 indicator explainers + the verified
pointer set are published education-only with "not medical advice" framing, **both HIGH-tier
sign-offs recorded**, WCAG 2.2 AA + readability met, comprehension ≥ 80%, and every figure
provenance-linked. (Cannot start until experts are secured.)

---

## Milestone M4 — Accessible visualization / dashboard

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-dashboard-017 | Static accessible dashboard over the open dataset (provenance + uncertainty shown) | code | medium | medium | pr | 011, 015 | Stats rev. + Maintainer |

**Acceptance criteria — screening-coverage-trends-dashboard-017**
- Static site built only on the open dataset; every chart provenance-linked and shows uncertainty;
  no chart implies causation or gives advice.
- WCAG 2.2 AA; works without a backend; statistical + accessibility review passed.

**M4 Definition of Done:** an accessible static dashboard reflecting the dataset with provenance +
uncertainty on every chart, no causal/advice implications, reviewed for stats + accessibility.

---

## Milestone M5 — Delivery & handoff (the deed)

> **Gated on a secured beneficiary.** Build-vs-mothball rule: if none by 2027-03-31, pivot last-mile
> to an OER/open-data channel or mothball to maintenance-only (recorded in governance).

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-partner-018 | Secure beneficiary + adoption plan (verifiedNeed flips true) | research | medium | medium | document | 010, 015, 017 | Steward + Maintainer |
| screening-coverage-trends-handoff-019 | Beneficiary adopts/uses a deliverable; record the outcome (the deed) | maintenance | medium | high | document | 018 | Steward + Clinician + Advocate |

**Acceptance criteria — key tasks**

- **screening-coverage-trends-partner-018** (secure beneficiary)
  - A named public-health program / advocacy org / newsroom / OER channel is secured; steward
    assigned; `verifiedNeed` flips to `true`; adoption plan + evidence definition recorded.
  - Driven by the dated acquisition track (beneficiary + experts); applies the build-vs-mothball rule
    if unmet by 2027-03-31.

- **screening-coverage-trends-handoff-019** (the deed)
  - The beneficiary demonstrably adopts/uses a deliverable with externally-verifiable evidence
    recorded by the steward; for any patient-facing piece used, clinician + advocate sign-off and the
    "not medical advice" framing are confirmed in the shipped form.

**M5 Definition of Done:** project-level **Definition of Shipped** met — a named beneficiary adopts/
uses a deliverable, evidence recorded; any patient-facing content used is expert-signed-off with
framing intact. *(Gated on a secured beneficiary — TO BE SECURED.)*

---

## Milestone M6 — Sustain, refresh & scale (post-delivery)

| ID | Title | Type | Size | Risk | Deliverable | Depends on | Reviewer |
|---|---|---|---|---|---|---|---|
| screening-coverage-trends-ops-020 | Refresh runbook + annual release cadence + re-review/re-verify cadence + maintenance rotation | maintenance | medium | medium | document | 019 | Maintainer + Steward + Clinician |

**Acceptance criteria — screening-coverage-trends-ops-020**
- Runbook covers data refresh from new source vintages (re-fetch→re-snapshot→re-run→re-license-check),
  annual release cadence, and version bumps.
- Content re-review cadence defined: guideline re-verification + re-sign-off (clinician + advocate) and
  helpline re-verification each release; staleness flags via recorded vintage/valid-until.
- Named maintenance rotation + steward; documented, gated process for adding an indicator, geography,
  or language (per-language review required).

**M6 Definition of Done:** project sustainably maintained — refresh + release cadence, content
re-review + helpline re-verification cadence, a named rotation/steward, and a gated expansion process.

---

## Backlog / future

| ID | Title | Type | Size | Risk | Deliverable | Notes |
|---|---|---|---|---|---|---|
| screening-coverage-trends-i18n-021 | Translate explainers into additional languages | translation | medium | high | translation | Per-language clinician + advocate review; HIGH tier |
| screening-coverage-trends-croissant-022 | Croissant ML metadata for the open dataset | code | small | low | pr | Defer/adopt decision (Open question) |
| screening-coverage-trends-intl-023 | Expand international comparisons to more licensed countries | data | large | medium | dataset | Per-source license gate each |
| screening-coverage-trends-county-024 | County/place small-area trends (PLACES, model-based) with strong caveats | data | medium | medium | dataset | Model-based caveats + small-cell guard mandatory |
| screening-coverage-trends-api-025 | Read-only static data API over the open dataset | code | small | low | pr | No PII; static; provenance preserved |
| screening-coverage-trends-newsroom-026 | "Explainer-to-article" reusable templates for newsrooms | writing | small | medium | document | Education-only; not advice; cite provenance |

---

## Generated task index

> Auto-generated by the Elyos task-decomposition agent on 2026-06-29.
> Seed task (001) was already present; tasks 002–026 were generated from the backlog above and
> validated against `packages/schema/src/schemas.ts`. All 26 files in `tasks/` are schema-valid.
> Fan-out dimensions: none (each backlog row maps to exactly one task JSON).

| File | Title | Type | Deliverable | Risk | Token | Status |
|---|---|---|---|---|---|---|
| [screening-coverage-trends-guardrails-001.json](tasks/screening-coverage-trends-guardrails-001.json) | Cancer data & compliance guardrail specification | design-spec | document | medium | medium | open |
| [screening-coverage-trends-sources-002.json](tasks/screening-coverage-trends-sources-002.json) | Source catalog v0 + per-source license classification + NC/SA policy decision | data | document | medium | medium | open |
| [screening-coverage-trends-repo-003.json](tasks/screening-coverage-trends-repo-003.json) | Repo skeleton (TS/ESM/pnpm) + CI with provenance-coverage & license-status gates | code | pr | low | small | open |
| [screening-coverage-trends-schema-004.json](tasks/screening-coverage-trends-schema-004.json) | Canonical trend-series schema + provenance model (JSON Schema) | code | pr | medium | medium | open |
| [screening-coverage-trends-gate-005.json](tasks/screening-coverage-trends-gate-005.json) | Apply gate end-to-end to ONE accepted source (US breast/mammography series) | data | dataset | medium | small | open |
| [screening-coverage-trends-outreach-006.json](tasks/screening-coverage-trends-outreach-006.json) | Beneficiary + expert-reviewer outreach (dated acquisition track) | research | document | medium | small | open |
| [screening-coverage-trends-pipeline-007.json](tasks/screening-coverage-trends-pipeline-007.json) | Fetch->snapshot->harmonize->trend+CI pipeline (one indicator) | code | pr | medium | large | open |
| [screening-coverage-trends-caveats-008.json](tasks/screening-coverage-trends-caveats-008.json) | Caveat layer (self-report/measured/model-based, break-in-series, suppression) | code | pr | medium | medium | open |
| [screening-coverage-trends-dataset-009.json](tasks/screening-coverage-trends-dataset-009.json) | First trend dataset: breast/mammography, US national + states (reproducible) | data | dataset | medium | medium | open |
| [screening-coverage-trends-datasheet-010.json](tasks/screening-coverage-trends-datasheet-010.json) | Data dictionary + Datasheet-for-Datasets + machine-readable metadata | writing | document | low | small | open |
| [screening-coverage-trends-indicators-011.json](tasks/screening-coverage-trends-indicators-011.json) | Add cervical, colorectal, lung indicators (US national + sub-national) | data | dataset | medium | large | open |
| [screening-coverage-trends-intl-012.json](tasks/screening-coverage-trends-intl-012.json) | Add >=1 licensed international comparison series | data | dataset | medium | medium | open |
| [screening-coverage-trends-disparities-013.json](tasks/screening-coverage-trends-disparities-013.json) | Suppression-safe disparities breakdowns (income/insurance/race-eth/urban-rural) | data | dataset | medium | medium | open |
| [screening-coverage-trends-explainer-014.json](tasks/screening-coverage-trends-explainer-014.json) | "How to read a screening statistic" explainer (education only) | writing | document | high | medium | open |
| [screening-coverage-trends-explainer-015.json](tasks/screening-coverage-trends-explainer-015.json) | >=2 indicator trend explainers (education only, guidance pointers) | writing | document | high | medium | open |
| [screening-coverage-trends-helpline-016.json](tasks/screening-coverage-trends-helpline-016.json) | Verified support/helpline + authoritative-guidance pointer set | research | document | high | small | open |
| [screening-coverage-trends-dashboard-017.json](tasks/screening-coverage-trends-dashboard-017.json) | Static accessible dashboard over the open dataset | code | pr | medium | medium | open |
| [screening-coverage-trends-partner-018.json](tasks/screening-coverage-trends-partner-018.json) | Secure beneficiary + adoption plan (verifiedNeed flips true) | research | document | medium | medium | open |
| [screening-coverage-trends-handoff-019.json](tasks/screening-coverage-trends-handoff-019.json) | Beneficiary adopts/uses a deliverable; record the outcome (the deed) | maintenance | document | high | medium | open |
| [screening-coverage-trends-ops-020.json](tasks/screening-coverage-trends-ops-020.json) | Refresh runbook + annual release cadence + re-review/re-verify cadence | maintenance | document | medium | medium | open |
| [screening-coverage-trends-i18n-021.json](tasks/screening-coverage-trends-i18n-021.json) | Translate explainers into additional languages | writing | translation | high | medium | open |
| [screening-coverage-trends-croissant-022.json](tasks/screening-coverage-trends-croissant-022.json) | Croissant ML metadata for the open dataset | code | pr | low | small | open |
| [screening-coverage-trends-intl-023.json](tasks/screening-coverage-trends-intl-023.json) | Expand international comparisons to more licensed countries | data | dataset | medium | large | open |
| [screening-coverage-trends-county-024.json](tasks/screening-coverage-trends-county-024.json) | County/place small-area trends (PLACES, model-based) with strong caveats | data | dataset | medium | medium | open |
| [screening-coverage-trends-api-025.json](tasks/screening-coverage-trends-api-025.json) | Read-only static data API over the open dataset | code | pr | low | small | open |
| [screening-coverage-trends-newsroom-026.json](tasks/screening-coverage-trends-newsroom-026.json) | "Explainer-to-article" reusable templates for newsrooms | writing | document | medium | small | open |

---

## Example task JSON

Complete, schema-valid Task JSON for the first M0 task (`screening-coverage-trends-guardrails-001`),
validated against `packages/schema/src/schemas.ts` (all required fields present; donated lane so no
`fundedBudgetUsd`):

```json
{
  "id": "screening-coverage-trends-guardrails-001",
  "title": "Cancer data & compliance guardrail specification (the contract all code/content implements)",
  "project": "screening-coverage-trends",
  "type": "design-spec",
  "lane": "donated",
  "priority": "high",
  "domain": ["cancer", "public-health", "open-data", "data-science", "health-communication"],
  "riskTier": "medium",
  "urgent": false,
  "deliverable": "document",
  "tokenEstimate": "medium",
  "status": "open",
  "context": "screening-coverage-trends turns OPEN, AGGREGATE cancer-screening-coverage data (CDC BRFSS/PLACES/NHIS, NCI/CDC State Cancer Profiles, UK OGL releases, Eurostat, OECD, AIHW, StatCan) into a harmonized open TREND dataset plus plain-language explainers. The dominant risks are legal (using a non-open/NC source as if open - e.g. WHO GHO is CC BY-NC-SA 3.0 IGO, GLOBOCAN/IARC is non-commercial), statistical (readers misreading self-report vs measured vs model-based estimates, changing denominators, suppressed small cells), and clinical (patient-facing content drifting into medical advice). The project is medium risk overall but its patient-facing surface is HIGH and ships only after oncologist + patient-advocate sign-off. This cold-start task writes the binding compliance/guardrail contract that all later code and content must implement and be tested against. No beneficiary partner and no expert reviewers are yet secured.",
  "objective": "Write the authoritative cancer data & compliance guardrail specification: the four binding guardrails (aggregate/de-identified/open-access data only; per-source license verification + provenance on every assertion; patient-facing = education-only HIGH tier with 'not medical advice' framing and blocking oncologist + patient-advocate sign-off; crisis/helpline info independently verified), the misread checklist, the no-causal-verb and not-medical-advice lints, the small-cell/re-identification rules, the NC/SA cite-only-or-exclude default, and the CI build-breakers + publish gate that tasks 003-005 and all M3 content must satisfy.",
  "acceptanceCriteria": [
    "Encodes the four binding guardrails as concrete, testable rules, including that controlled-access and individual-level/identifiable data are out of scope and excluded at triage (aggregate/de-identified/open-access only)",
    "Requires per-source license verification with a recorded license id + URL + clause and explicit permitsDerivatives evidence before any redistribution; missing/unclear/NC-SA terms default to cite-only or exclude, never default-allow",
    "Requires provenance on every published assertion and chart, enforced as a 100% CI provenance-coverage build-breaker, plus license-status, schema-validity, reproducible-from-snapshots, small-cell-guard, and helpline-verification CI gates",
    "Defines the patient-facing HIGH-tier rules: education only, persistent 'general information, not medical advice - consult your care team' notice, no personalized/eligibility/diagnosis framing, guideline statements attributed to USPSTF/ACS/national bodies with version/date, and BLOCKING oncologist + patient-advocate sign-off recorded before publish",
    "Defines the misread checklist (denominator stated; basis self-report/measured/model-based labeled; model-based small-area flagged; no causal verbs on associational data; no advice verbs; guideline body cited) and the no-causal-verb and not-medical-advice lints",
    "Defines the small-cell / re-identification rules: honor source suppression, apply an own min-cell + min-denominator threshold, suppress-and-label never impute, and no cross-dataset linkage",
    "Requires crisis/helpline/support pointers to be independently verified (number, region, hours, language) with a dated verification record and a re-verification cadence",
    "Reviewed and recorded as the contract that screening-coverage-trends-repo-003, -schema-004, -gate-005 and all M3 patient-facing content implement and are tested against"
  ],
  "resources": [
    "planning/projects/screening-coverage-trends/PLAN.md",
    "CLAUDE.md",
    "docs/good-deed-definition.md",
    "packages/schema/src/schemas.ts",
    "planning/ROADMAP.md",
    "planning/projects/open-data-datasheets/PLAN.md"
  ],
  "output": "A reviewed compliance/guardrail specification document defining the four binding cancer guardrails, the license + provenance rules (incl. NC/SA cite-only-or-exclude default), the misread checklist + lints, the small-cell/re-identification rules, the verified-helpline rule, the CI build-breakers, and the HIGH-tier publish gate with blocking oncologist + advocate sign-off - the contract that the repo/CI, canonical schema, source gate, and all patient-facing content implement and are verified against.",
  "requestor": "TO BE SECURED",
  "verifiedNeed": false,
  "outputLicense": "CC-BY-4.0"
}
```
